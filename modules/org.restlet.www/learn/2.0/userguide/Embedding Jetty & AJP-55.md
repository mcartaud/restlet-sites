Embedding Jetty & AJP
=====================

Introduction
============

Here are a few links that will get you started:

-   [What's AJP(Apache JServ
    Protocol)?](http://web.archive.org/web/20100913044456/http://en.wikipedia.org/wiki/Apache_JServ_Protocol)
-   [What's Apache module
    mod\_jk?](http://web.archive.org/web/20100913044456/http://tomcat.apache.org/connectors-doc/webserver_howto/apache.html)
-   [What's Jetty,and how to get
    it?!](http://web.archive.org/web/20100913044456/http://www.mortbay.org/)
-   [How to embedding Jetty in pragram
    way?](http://web.archive.org/web/20100913044456/http://docs.codehaus.org/display/JETTY/Embedding+Jetty)

Description
===========

Embedding Jetty
---------------

### JettyAJPApplication.Class

    package com.bjinfotech.restlet.practice.demo.microblog;

    import org.restlet.Application;
    import org.restlet.Component;
    import org.restlet.Directory;
    import org.restlet.Restlet;
    import org.restlet.Router;
    import org.restlet.Server;
    import org.restlet.data.Protocol;
    import com.noelios.restlet.ext.jetty.AjpServerHelper;
    import com.noelios.restlet.ext.jetty.HttpServerHelper;
    import com.noelios.restlet.ext.jetty.JettyServerHelper;

    public class JettyAJPApplication extends Application {
        public static void main(String[] argv) throws Exception{
            Component component=new Component();

            Application application=new Application(component.getContext()){
                @Override
                public Restlet createRoot(){
                    final String DIR_ROOT_URI="file:///E:/eclipse3.1RC3/workspace/RestletPractice/static_files/";

                    Router router=new Router(getContext());
                    Directory dir=new Directory(getContext(),DIR_ROOT_URI);
                    dir.setListingAllowed(true);
                    dir.setDeeplyAccessible(true);
                    dir.setNegotiateContent(true);
                    router.attach("/www/",dir);
                    return router;
                }
            };

            ...
            //create embedding jetty server
            Server embedingJettyServer=new Server(
                    component.getContext(),
                    Protocol.HTTP,
                    8182,
                    component
                );
            //construct and start JettyServerHelper
            JettyServerHelper jettyServerHelper=new HttpServerHelper(embedingJettyServer);
            jettyServerHelper.start();

            //create embedding AJP Server
            Server embedingJettyAJPServer=new Server(
                    component.getContext(),
                    Protocol.HTTP,
                    8183,
                    component
                );

            //construct and start AjpServerHelper
            AjpServerHelper ajpServerHelper=new AjpServerHelper(embedingJettyAJPServer);
            ajpServerHelper.start();

        }
    }

### Running JettyAJPApplication

> 2008-02-13 15:33:54.890::INFO:  Logging to STDERR via
> org.mortbay.log.StdErrLog\
>  2008-02-13 15:33:54.953::INFO:  jetty-6.1.x\
>  2008-02-13 15:33:55.140::INFO:  Started SelectChannelConnector @
> 0.0.0.0:8182 \
>  2008-02-13 15:33:55.140::INFO:  jetty-6.1.x\
>  2008-02-13 15:33:55.140::INFO:  AJP13 is not a secure protocol.
> Please protect port 8183\
>  2008-02-13 15:33:55.140::INFO:  Started Ajp13SocketConnector @
> 0.0.0.0:8183

Configuring Apache HTTPD server with mod\_jk
--------------------------------------------

-   put mod\_jk.so into your \<apache-root\>/modules/ directory
-   you can download mod\_jk.so here
    [http://archive.apache.org/dist/tomcat/tomcat-connectors/jk/binaries/](http://web.archive.org/web/20100913044456/http://archive.apache.org/dist/tomcat/tomcat-connectors/jk/binaries/)
-   add the entry below in your httpd.conf apache configuration file
    located in \<apache-root\>/conf/ directory:

            <IfModule !mod_jk.c>

                 LoadModule jk_module  modules/mod_jk.so

            </IfModule>

            <IfModule mod_jk.c>

                 JkWorkersFile "conf/worker.properties"

                 JkLogFile "logs/mod_jk.log"

                 JkLogLevel info

                 JkLogStampFormat "[%a %b %d %H:%M:%S %Y] "

                 JkOptions +ForwardKeySize +ForwardURICompat

            </IfModule>   

-   **LoadModule jk\_module modules/mod\_jk.so** tells your apache
    server to load the mod\_jk libray and where it is located.
-   **JkWorkersFile conf/worker.properties** tells mod\_jk where your
    worker.properties is located.
-   **JkLogFile logs/mod\_jk.log** tells mod\_jk where to write mod\_jk
    related Logs.

After adding the mod\_jk configuration you may add a **VirtualHost**
Entry in the same file (httpd.conf) as long as its located below your
mod\_jk configuration entry:

>         <VirtualHost host:*>
>
>             ServerName yourserver
>
>              ServerAdmin user@yourserver
>
>              ## you may add further entries concerning log-files, log-level, URL-rewriting, ...
>
>              ## pass requests through to jetty worker
>
>         JkMount /* jetty
>
>         </VirtualHost>

-   Add a worker file **worker.properties** in your
    \<apache-root\>/conf/
-   add the entries below, and make sure to specify your ip-address or
    hostname in **worker.jetty.host** property entry to where your jetty
    application is runnning

> `worker.list=jetty`
>
> `worker.jetty.port=8009`
>
> `worker.jetty.host=<server name or ip where your jetty will be running>`
>
> `worker.jetty.type=ajp13`

mod\_jk Compatibilities
-----------------------

> Apache
>
> mod\_jk
>
> Win32
>
> Linux(ubuntu)
>
> Apache 1.3
>
> No HTTPD Binary Available
>
> mod\_jk-1.2.14
>
> Not yet tested
>
> mod\_jk-1.2.15
>
> Not yet tested
>
> mod\_jk-1.2.18
>
> Not yet tested
>
> mod\_jk-1.2.19
>
> Not yet tested
>
> Apache 2.0 (2.0.59)
>
> mod\_jk-1.2.14
>
> ![](Embedding%20Jetty%20&%20AJP-55_files/check.gif)
>
> mod\_jk-1.2.15
>
> ![](Embedding%20Jetty%20&%20AJP-55_files/check.gif)
>
> mod\_jk-1.2.18
>
> ![](Embedding%20Jetty%20&%20AJP-55_files/check.gif)
>
> mod\_jk-1.2.19
>
> ![](Embedding%20Jetty%20&%20AJP-55_files/check.gif)
>
> Apache 2.2
>
> mod\_jk-1.2.14
>
> No Binary Available
>
> mod\_jk-1.2.15
>
> No Binary Available
>
> mod\_jk-1.2.18
>
> ![](Embedding%20Jetty%20&%20AJP-55_files/check.gif)
>
> mod\_jk-1.2.19
>
> ![](Embedding%20Jetty%20&%20AJP-55_files/check.gif)

Running Apache Httpd and test your application
----------------------------------------------

[To be detailled]

More Resource
-------------

[Jetty
doc:Configuring+AJP13+Using+mod\_jk](http://web.archive.org/web/20100913044456/http://docs.codehaus.org/display/JETTY/Configuring+AJP13+Using+mod_jk)
\
 [Apache Httpd
Document](http://web.archive.org/web/20100913044456/http://httpd.apache.org/docs/)
\
 [HttpServerHelper Class
API](http://web.archive.org/web/20100913044456/http://www.restlet.org/documentation/1.1/ext/com/noelios/restlet/ext/jetty/HttpServerHelper.html)
\
 [Server Class
API](http://web.archive.org/web/20100913044456/http://www.restlet.org/documentation/1.1/api/org/restlet/Server.html)
\
 [AjpServerHelper Class
API](http://web.archive.org/web/20100913044456/http://www.restlet.org/documentation/1.1/ext/com/noelios/restlet/ext/jetty/AjpServerHelper.html)

[Comments
(0)](http://web.archive.org/web/20100913044456/http://wiki.restlet.org/docs_2.0/13-restlet/28-restlet/78-restlet/55-restlet.html#)

Comments
[Hide](http://web.archive.org/web/20100913044456/http://wiki.restlet.org/docs_2.0/13-restlet/28-restlet/78-restlet/55-restlet.html#)
\
There are no comments.

Add a comment

Please log in to be able to add comments.