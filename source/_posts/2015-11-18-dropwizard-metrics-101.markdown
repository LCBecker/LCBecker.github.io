---
layout: post
title: "DropWizard Metrics 101"
date: 2015-11-18 18:18:54 -0500
comments: true
categories: Java DropWizard
---
{% img center /images/metrics.jpg 'metrics art' 'metrics art' %}
###### IO Graph Art by [nathanmac87](http://www.flickr.com/photos/nathanmac87/4415951740) ######


**Post revised 11/23/2015 for correctness. Thank you to Jan-Olav Eide and Tim Bart for leading me in the right direction!**

My most recent project at work has been to utilize DropWizard metrics to gather information about an application I built and send that data to Graphite to display in a realtime dashboard. As I normally do, I reviewed the documentation for [Dropwizard Metrics](https://dropwizard.github.io/metrics/3.1.0/getting-started/), but as usual, there was a lot to be desired. Their example did not go into the nitty gritty of _how_ to connect everything together. The internet was also silent on how to actually use DropWizard metrics.

So here is my tutorial on how _I_ was able to get DropWizard metrics up and running in my application:

### 1. Maven Application? Update pom.xml ###
If you've got a Maven application, you'll need to add metrics-core as a dependency. If you're using some sort of special reporter like Graphite, you may need to add additional dependencies. This was what I had to add to my pom:

    <dependency>
    	<groupId>io.dropwizard.metrics</groupId>
    	<artifactId>metrics-core</artifactId>
    	<version>3.1.0</version>
    </dependency>
    <dependency>
    	<groupId>io.dropwizard.metrics</groupId>
    	<artifactId>metrics-graphite</artifactId>
    	<version>3.1.0</version>
    </dependency>
    <dependency>
        <groupId>io.dropwizard</groupId>
        <artifactId>dropwizard-metrics-graphite</artifactId>
        <version>0.9.1</version>
    </dependency>
    
### 2. Start Your Metrics On Application Startup ###
Supposedly, there are two ways you can initialize your metrics reporters. A reporter sends your data to either the console, logger, Graphite, or other DropWizard-approved outputs. You can either specify them in your config.yml file, as seen [here](http://stackoverflow.com/questions/29432586/dropwizard-yaml-for-graphite-server-configuration). This is supposed to start reporting your metrics automatically when you start your application. I tried this method, but I had zero luck with it. I ended up getting some parsing error from within DropWizard's validation library.

So, I initialized my metrics an alternative way-- in the `run()` method of my application class. DropWizard's environment already creates a `MetricRegistry` object on startup. This object manages all of our metrics (these are timers, counters, meters, histograms, and gauges). Since I added metrics to my resources class, I needed to pass that `MetricRegistry` object to my resource class. This is how my application class looked:

    public class MyApplication extends Application<MyConfiguration>{

        public static void main(String[] args) throws Exception {
            new MyApplication().run(args);
        }

        @Override
        public void run(MyConfiguration config, Environment env) throws Exception {

            final Graphite graphite = new Graphite(new InetSocketAddress("my.graphite.host", 2003));

            final GraphiteReporter reporter = GraphiteReporter.forRegistry(env.metrics())
                .prefixedWith("upToYou")
                .convertRatesTo(TimeUnit.SECONDS)
                .convertDurationsTo(TimeUnit.MILLISECONDS)
                .filter(MetricFilter.ALL)
                .build(graphite);
            reporter.start(5, TimeUnit.SECONDS);
            environment.jersey().register(new MyResource(env.metrics()));
        }
    }
    
Some things to note: The `prefixedWith` is not required. What this does is append a label in front of our metrics names. I'll explain this more in Step 3.


### 3. Add Metrics To Your Resources ###
I wanted metrics like 'How many requests are we getting at /path1' and 'How long is it taking for the method handling /path1 to return a response'. Chances are you are, too. In order to do this, we'll need to add metrics to our resources. And those metrics need to be "saved" in the `MetricRegistry` from Step 2. So we'll need to create a constructor for our resource that will take in the `MetricRegistry` object we passed in Step 2. 

There are two ways to add metrics to your resources: using annotations and by actually creating objects of a metrics class. I'll give you an example of both: 


    public class MyResource {
         MetricRegistry metrics;
         Meter requestCount;
         
         public MyResource(MetricRegistry registry) {
         	this.metrics = registry;
         	requestCount = metrics.meter(MetricRegister.name("requestCount"));
         }
         
         @POST
         @Path("/path1")
         @Produces(MediaType.TEXT_PLAIN)
         @Timed(absolute=true, name="requestRuntime")
         public Response handleRequest() {
         	requestCount.mark();
         	return Response.ok("Hello World").build(); //you know what I mean...
         }
    }

Notice these details: 

- We declare the meter `requestCount`, which will give us the total count of all calls made to /path1 and how many requests /path1 gets in 1 minute, 5 minute, and 15 minute timeframes. We initialized this `Meter` object in our Resource constructor, It's initialization uses our `MetricRegistry` so it can manage it accordingly.
- See the `@Timed` annotation? DropWizard will automatically create a timer and whenever `handleRequest()` is called, the timer will record how long it takes to complete this method. We do not need to initialize a timer object ourselves. We can name this timer. I've added the `absolute=true` field, because otherwise, the entire path of this metric in Graphite will be my prefix + package path + class + requestRuntime. Too long! By setting absolute to true, the metric path is just the prefix + the metric name. 
- Remember in Step 2 when we saw that `.prefixedWith("upToYou")`? Basically, our reporter, whether Graphite or the console or something else, will refer to this metric by the prefix + name of the metric. Graphite showed me options to graph like, "upToYou.requestCount.count" and "upToYou.requestCount.mean" (the 'count' and 'mean' part are automatically attached by DropWizard metrics). You don't need any prefix, but if you want it, go for it back in Step 2.
- Finally, we call our meter's `mark()` method. That tells the metric, "Hey, the method we're tracking has been called! Increment the meter count!" The `mark()` method is for the meter metric, though. Please refer to the [DropWizard Metrics](https://dropwizard.github.io/metrics/3.1.0/getting-started/) documentation on how to initialize and call counters, histograms, and other metrics. Now, here we've programmatically called the meter's method. We don't have to do that. We could just use the annotation `@Metered(absolute=true, name="myMeter")` and DropWizard would automatically call `mark()` for us whenever our method was called. But this is how you would do it if you wanted additional control...or multiple meters on a single method.

### 4. Profit ###

Now, when we start up our application, our metrics will be monitoring what we want and sending data to the console or Graphite or wherever! 

So, there you have it! This is how everything ties together in DropWizard Metrics! And I'm the first person to actually write an example of this! Woohoo! I hope you have enjoyed this post and that it's given you a decent amount of confidence to use DropWizard metrics in your application! Happy Coding!

If you really like this, tell me on Twitter @LBeckerCodes !