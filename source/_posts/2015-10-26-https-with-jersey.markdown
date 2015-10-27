---
layout: post
title: "HTTPS With Jersey"
date: 2015-10-26 20:16:51 -0400
comments: true
categories: java jersey REST
---
I am so excited about this post! It's a technical post! With code! I wanted to write this to help others who might find themselves in the same predicament.

This was the issue: I'm writing a DropWizard application. DropWizard uses Jersey. My application needed to take in a POST request and use the post body to make a POST request to a trusted server using HTTPS. But then this error showed up:

    ERROR [2015-10-22 20:41:23,405] io.dropwizard.jersey.errors.LoggingExceptionMapper: Error handling a request: 8d10b79e51bbb14d
    ! java.lang.IllegalStateException: AlreadyConnected
    ! at sun.net.www.protocol.http.HttpsUrlConnection.setRequestProperty(Unknown Source)...
    
After doing a little Googling, I found out that the exception is mislabeled. Jersey shouldn't be throwing an IllegalStateException. It should be a SSLHandshakeException. Ok, so the question is: What's the workaround? We need to get Jersey (version 2) to accept a self-signed SSL certificate. This requires creating a class with a method we can call to help build a client with the needed configurations. I created this class exactly as Aleksandr Panzin demonstrated [here](https://gist.github.com/JAlexoid/b15dba31e5919586ae51).

Once I added the `JerseyWithSSL` class, I realized, "Hey, Aleksandr didn't explain how to actually use it!" And so now, I provide to you the complete solution! Back in my resources class, I created a client using the `JerseyWithSSL` method `initClient()`:

    import java.security.KeyManagementException;
    import java.security.NoSuchAlgorithmException;
    import javax.ws.rs.POST;
    import javax.ws.rs.client.Client;
    import org.glassfish.jersey.client.ClientConfig;

    @Path("/ticket")
    public class Ticket
    
    @POST
    public Response createTicket(...) {
    	ClientConfig cc = new ClientConfig();
    	Client client = null;
    	try {
    		client = new JerseyWithSSL().initClient(cc);
        } catch (KeyManagementException e) {
            e.printStackTrace();
        } catch (NoSuchAlgorithmException e) { 
            e.printStackTrace();
        }
    
    // Use client as you need...
    
The trick was figuring out what exactly the `Configuration` parameter was supposed to be in the `initClient()` method. An instance of `ClientConfig` did the trick. I'm happy to say my application works due to this code! Hopefully, it will help you if you ever come across this error.

Happy Coding!
