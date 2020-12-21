---
layout: post
title:  "Deploying a single page website to Google Cloud Platform"
---

Having never used Google Cloud Platform (GCP) I thought deploying a single page website would be a good way to learn, there is a handy step by step guide by Google [here](https://cloud.google.com/storage/docs/hosting-static-website#gsutil). I will talk through the experience and any issues encountered.

Before starting I added a TXT record to the DNS settings for the domain name, this allows Google to validate ownership of the site.

The [guide](https://cloud.google.com/storage/docs/hosting-static-website#gsutil) turned out to be straightforward, the sum total of the setup commands were:

    ```
    # make a bucket for the site
    gsutil mb -b on gs://blog.danielholden.org

    # copy site to bucket
    gsutil cp index.html gs://blog.danielholden.org
    gsutil cp 404.html gs://blog.danielholden.org

    # set permissions on the bucket
    gsutil iam ch allUsers:objectViewer gs://blog.danielholden.org

    # set main and error page for the site
    gsutil web set -m index.html -e 404.html gs://blog.danielholden.org
    ```

The load balancer required setup via the UI, I will initially do this via the UI but then look to do this via the CLI and eventually automate this via a pipeline.

The HTTPS certificate creation process was simple and allowed for the creation of subject alternative names, I used:
- danielholden.org
- blog.danielholden.org

Once the load balancer is created we take the IP address and to add as an `A` record in our DNS settings, this will take a while to propagate and can be verified with `nslookup`

```
nslookup danielholden.org
Server:         [redacted]
Address:        [redacted]#53

Non-authoritative answer:
Name:   danielholden.org
Address: 35.186.248.145
```

A few minutes later and the site is live

![live site]({{ site.url }}assets/1-site.png)

Notice the lock icon, indicating the site is secured using HTTPS

![live site]({{ site.url }}assets/1-https.png)

The documentation is clean and the process was smooth, before moving on I will create a simple HTML page from the markdown of this article and upload that in place of the _test page_. For the sake of completeness, here are the steps I followed to convert a markdown document to HTML using [jekyll](https://jekyllrb.com/docs/).

```
# install jekyll
gem install jekyll bundler

# create blog
jekyll new blog
cd blog

# convert markdown to html
bundle exec jekyll build
```

Let's update the site with our new resources
```
# copy site to bucket
gsutil cp * gs://blog.danielholden.org
```

Once uploaded the new site did not appear, I believe this is Google's Content Delivery Network (CDN) doing it's thing, using the following command invalidates the CDN cache and voilà the new site loads

```
gcloud compute url-maps invalidate-cdn-cache danielholden-lb \
    --host danielholden.org \
    --path "/*"
```

![article 1]({{ site.url }}assets/1-article.png)

In the next article I will aim to automate the creation of the entire site.
