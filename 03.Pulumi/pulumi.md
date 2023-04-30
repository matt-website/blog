# Getting Started with Webhosting
 
## Starting the journey
It's a little bit daunting if you're not a web developer and you're suddenly tasked with getting a webpage out there that people can browse to. On top of this I knew that my eventual plan was for my website to be capable of serving a mixture of content.

I decided I wanted to utiilise MarkDown for the more blog style pages - aside from being a RichText editor, it supports a few different features I need (Namely Latex and iFrames) as well as meaning I wasn't tied in to any particular CMS system (Such as wordpress) which I values as I knew I would probably make at least a couple of mistakes when deciding on the hosting stack, and being able to just point at an MD file that I am iterating on in Git would be an easier task.

I realised that Wordpress has a few plugins to render Markdown, but came across Grav https://learn.getgrav.org/ during my google journey which I hadn't heard of before.

It purports to be a stripped down CMS system, authored through MarkDown, but (in its own words) 

> "There are many powerful open source CMS solutions for building complex websites. Some of the more commonly used ones are Joomla, WordPress, and Drupal. The downside of these platforms is that they have a steep learning curve associated with them. This requires a significant amount of your time - and this may be the time that you do not have."

Which appealed to me, so I decided to do my website V1 using Grav.

The other requirements I have are to be able to serve Unity WebGL projects which exist on an AWS S3 bucket. And embed them into the Markdown documents via \<iFrames\> so that I am able to render a Uniy project in the middle of a blog post.

However Unity rendering is stage 2. So first I just want to get a Markdown page rendered on a local server.

### Step 1 - Getting a local server up and running with Grav

Grav actually comes with a built in PHP server (https://learn.getgrav.org/16/webservers-hosting/servers/grav-built-in) so I started focussing my attention here.

It was super easy to kick off the webserver, and browse to my first page - the documentation works a treat so I'd point anyone reading this there.

### Step 2 - Run grav from an AWS EC2 server

I didn't have any preconceptions about how or where to host a grav server. So by default I decided to go with AWS EC2 as I knew I was planning to utilise S3 for my Unity project builds - and the mental overhead of sticking to one ecosystem felt better than trying to learn GCP, or a dedicated webhost, simultaneously.

I'd also been lucky enough to work with the talented Lucas Seidenfaden in the past, a full stack developer who helped me learn the ropes of AWS. Lucas highly rated the route of Configuration as Code for managing AWS. I decided to adopt and learn https://www.pulumi.com/

Pulumi felt like an interesting choice because it supported C# as an input language. As this blog will mostly be covering c# concepts due to Unity, and given that I am familiar with the language, I decided to target C# and Pulumi, leaving a future iteration in typescript as a stretch goal.

### Step 3 - Configuring an EC2 server via Pulumi

1.) Setup AWS with correctly configure CLI access https://docs.aws.amazon.com/cli/latest/userguide/getting-started-prereqs.html
2.) Install pulumi and follow the "my first pulumi" guide here https://www.pulumi.com/docs/get-started/aws/begin/
3.) Followed the whole guide and saw a static website being hosted, that was cool

4.) Follow EC2 Webserver guide to get Grav up and running: https://www.pulumi.com/registry/packages/aws/how-to-guides/ec2-webserver/

![testmov](testmov.mov)


 So the next stage was how to get this running on an ACTUAL webserver, where to host, and how to point a URL to it...

### Step 4 a docker image which contains grav and pulls our content

So now we're going to use a docker image to prop a EC2 t2.micro server with
This docker image contains a websever (apache) grav (CMS) and the build stages fetches from our documentation git repository for the blogs content
Using pulumi we utilise ECS (where we can store the image) - 

You can use this docker image file to get started.

The main additions I added were

### Add github to list of known hosts
    RUN mkdir /root/.ssh
    RUN touch /root/.ssh/known_hosts
    RUN ssh-keyscan github.com >> /root/.ssh/known_hosts
    
### clone into the pages directory
    RUN git clone https://github.com/matt-website/blog.git /var/www/html/user/git-blog
    RUN cp -r /var/www/html/user/git-blog/* /var/www/html/user/pages

You can download this docker file directly from:


## Step 5 More Pulumi!

Okay now let's go indepth into the pulumi script which performs the entire process of getting our website hosted

### A

    // Create an AWS resource (S3 Bucket)
    var repository = new Awsx.Ecr.Repository("grav-repository", new RepositoryArgs()
    {
    ForceDelete = true, // Ensures pulumi delete runs correctly
    });

<div>
  <iframe id="inlineFrameExample"
      title="Inline Frame Example"
      width="300"
      height="200"
      src="https://commons.wikimedia.org/wiki/File:HelloWorld.svg">
  </iframe>
</div>