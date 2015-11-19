---
layout: post
title: "DropWizard Metrics 101"
date: 2015-11-18 18:18:54 -0500
comments: true
categories: Java DropWizard
---
{% img center /images/metrics.jpg 'metrics art' 'metrics art' %}
###### IO Graph Art by [nathanmac87](http://www.flickr.com/photos/nathanmac87/4415951740) ######

My most recent project at work has been to utilize DropWizard metrics to gather information about an application I built and send that data to Graphite to display in a realtime dashboard. As I normally do, I reviewed the documentation for [Dropwizard Metrics](https://dropwizard.github.io/metrics/3.1.0/getting-started/), but as usual, there was a lot to be desired. Their example did not go into the nitty gritty of _how_ to connect everything together. The internet was also silent on how to actually use DropWizard metrics.

So here is my tutorial on how _I_ was able to get DropWizard metrics up and running in my application:

### 1. Maven Application? Update pom.xml ###
If you've got a Maven application, you'll need to add metrics-core as a dependency. If you're using some sort of special reporter like Graphite, you may need to add an additional dependency. This was what I had to add to my pom:

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

### 2. Make a Metrics Class ###
The metrics class we're going to create is going to have a `MetricRegistry` object that will manage all of our metrics (these are timers, counters, meters, histograms, and gauges). The metrics class has a method to retrieve the `MetricRegistry` because this is needed to register the meters we will define in our Resources class(es). Finally, it contains a method to report what all of our metrics collect to some output. I sent my data to Graphite. Note that the `prefixedWith` is not really necessary. What this does is append a label in front of our metrics names. I'll explain this more in Step 3.  This is what my Metrics class looked like:

	import java.net.InetSocketAddress;
	import java.util.concurrent.TimeUnit;
	import com.codahale.metrics.MetricFilter;
	import com.codahale.metrics.MetricRegistry;
	import com.codahale.metrics.graphite.Graphite;
	import com.codahale.metrics.graphite.GraphiteReporter;
	
	public class MyAppMetrics() {
		static final MetricRegistry metrics = new MetricRegistry();
		
		public static MetricRegistry getRegistry() {
			return metrics;
		}
		
		public static void startReport() {
			final Graphite graphite = new Graphite(new InetSocketAddress("your.graphite.com", 2003));
			final GraphiteReporter reporter = GraphiteReporter.forRegistry(metrics)
			.prefixedWith("upToYou")
			.convertRatesTo(TimeUnit.SECONDS)
			.convertDurationTo(TimeUnit.MILLISECONDS)
			.filter(MetricFilter.ALL)
			.build(graphite);
			reporter.start(1, TimeUnit.MINUTES);
		}
	}

### 3. Add Metrics To Your Resources ###
I wanted metrics like 'How many requests are we getting at /path1' and 'How long is it taking for the method handling /path1 to return a response'. Chances are you are, too. In order to do this, we'll need to add metrics to our resources. And those metrics need to be "saved" in the `MetricRegistry` from Step 2. So we'll need to create a constructor for our resource that will take in the `MetricRegistry` object we've got in our metrics class. In addition, we will need to instantiate our metrics inside your Resources constructor. And finally, we need to link those metrics to a method in our resource. Take a look at the following code:


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
         @Metered(name="requestCount")
         public Response handleRequest() {
         	requestCount.mark();
         	return Response.ok("Hello World").build(); //you know what I mean...
         }
    }

Notice these details: 

- We declare the meter `requestCount`, which will give us the total count of all calls made to /path1 and how many requests /path1 gets in 1 minute, 5 minute, and 15 minute timeframes. This meter is initialized in our Resource constructor, but you could put it in some sort of `init()` method if you prefer. It's initialization uses our `MetricRegistry` so it can manage it accordingly. 
- Next, we've placed the annotation `@Metered(name="requestCount")` above the method we'd like to monitor. The name part is just that, a name. Remember in Step 2 when we saw that `.prefixedWith("upToYou")`? Basically, our reporter, whether Graphite or the console or something else, will refer to this metric by the prefix + name of the metric. Graphite showed me options to graph like, "upToYou.requestCount.count" and "upToYou.requestCount.mean" (the 'count' and 'mean' part are automatically attached by DropWizard metrics). You don't need any prefix, but if you want it, go for it.
- Finally, we call our meter's `mark()` method. That tells the metric, "Hey, the method we're tracking has been called! Increment the meter count!" The `mark()` method is for the meter metric, though. Please refer to the [DropWizard Metrics](https://dropwizard.github.io/metrics/3.1.0/getting-started/) documentation on how to initialize and call counters, histograms, and other metrics.

### 4. Start Your Metrics On Application Startup ###
We've almost come full circle! The last step is to simply provide our `MetricRegistry` in our Metrics class to our Resource and get our metrics to be on the lookout when we start our application. In the `run()` method in our `Application` class:

    environment.jersey().register(new com.example.resource.MyResource(MyAppMetrics.getRegistry()));
    MyAppMetrics.startReport();

Now, when we start up our application, our metrics will be monitoring what we want and sending data to the console or Graphite or wherever! 

So, there you have it! This is how everything ties together in DropWizard Metrics! And I'm the first person to actually write an example of this! Woohoo! I hope you have enjoyed this post and that it's given you a decent amount of confidence to use DropWizard metrics in your application! Happy Coding!

If you really like this, tell me on Twitter @LBeckerCodes !