<h3 align="center"> 95-702 Distributed Systems <br><br>
  Lab 4 <br><br>
  HTTP Server Lab<br><br></h3>
This lab builds on the discussion of HTTP and networking protocols in the
lectures and has you work with a transport layer protocol and implement some
basic elements of an application layer protocol.<br><br>
You should first view the 13 minute video that explains TCP and UDP sockets
and introduces the task for this lab.<br>
http://heinzcollege.mediasite.com/Mediasite/Play/21d1939d54f04444b042e45fc923742e1d

<br><br>
:warning:<b> If you have reached this point, and you have not watched the video, go back.</b>

<br><br>
EchoServerTCP.java, and all the client and server files can be found at:<br>
http://www.andrew.cmu.edu/course/95-702/examples/sockets/<br><br>
For this lab you are to build your own web server.  Just like Apache, IIS,
or nginx, you are to build a web server that can take HTTP requests from a
browser and return static HTML files.  (Of course, your server will be much
simpler than Apache and the rest.)<br><br>
You should start with the EchoServerTCP.java class, and change it to be able
to handle simple HTTP GET requests for static html files.<br><br>
You should develop your HTTP Server in IntelliJ IDEA so that you can continue
to get comfortable using the IDE. Note that you are not developing a Web
Application; rather you are developing a simple Java desktop application.
Explore how do create a simple Java desktop application in IntelliJ, and help
those around you to figure it out also. (You are encouraged to help each other
for Labs, but doing so is taboo for Projects.)<br><br>
Your Java application should be based on EchoServerTCP.java (i.e. copy that code
to start with), but should have a class name that makes sense for its role as a
simple web server. <br><br>
You will also have to figure out how to create a Run Configuration for a Java
desktop app.  You run it as you would any Java application. It does not use
TomEE.<br><br>
You will make requests to your simple HTTP server using a browser.  You do not
need to develop an HTTP client.<br>

##### So to review so far:
  * The web browser (e.g. Chrome) will be your client.
  * You are to create a web server, using EchoServerTCP.java as a starting point

<br>
EchoServerTCP.java is not currently a web server. It is a silly application
that just echoes back to the sender whatever it received.  <br>

##### Like a web server, EchoServerTCP.java can:
  * Open a TCP socket from a client
  * Read data arriving on the socket
  * Write data to the socket

<br>
But unlike a web server, when EchoServerTCP.java reads from the socket, it
writes the same data back to the socket.  This is not the HTTP protocol.<br>
 
##### What a web server does but EchoServerTCP.java does NOT currently do is:
  * Parse the data being read and interpret it as an HTTP request
  * Write a valid HTTP response header to the socket
  * Read the file the HTTP request asked for from local disk
  * Write the file data the HTTP request asked for to the socket

<br>
Your task in this lab is to understand EchoServerTCP.java, and then use it to 
help you develop a web server. <br><br>
Code is poetry. Read and work to understand each line of EchoServerTCP.java as
if you were learning and understanding a poem. Discuss each line with those
around you so that you together fully understand the code. If you first
understand the EchoServerTCP.java poem, it will be much easier to build the web
server.<br><br>
Your HTTP server should be able to handle multiple requests, one-at-a-time. You
do <b>not</b> have to handle multiple simultaneous requests, so your solution can be
single-threaded. (I.e. you do not have to use threads.)<br>

##### In general terms, your simple HTTP server should:
Create a socket
  * Forever
    * accept a socket connection
    * read the first line of the HTTP request
      * parse out the file path requested<br>
            :checkered_flag:<b>(CHECKPOINT: print the file path to the console.)</b>
      * read but discard the rest of the request headers
                  so that the next line read would be the next HTTP 
                  request, not a prior header
    * open the file requested on your local disk
      * if unsuccessful, report file not found using the correct HTTP status
                  code and go to next request
      * else...
      * reply with OK status response header
      * reply with blank line to indicate end of headers
      * while more lines of the file remain...
        * read a line of the file
        * write to the socket
      * flush the socket
      * close the socket
<br>

#### Hints:
  * <b>HTTP REQUEST</b>:
    * You should review the Server Side Programming slides from week 3 to review
        the format of simple HTTP requests.
              E.g. In Chrome, here is an HTTP request of http://localhost:3000/test.html
                  GET /test.html HTTP/1.1
                  Host: localhost:3000
                  Connection: keep-alive
                  Cache-Control: max-age=0
                  Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
                  User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_8_2)  ...
                  Accept-Encoding: gzip,deflate,sdch
                  Accept-Language: en-US,en;q=0.8
                  Accept-Charset: ISO-8859-1,utf-8;q=0.7,*;q=0.3
    * A blank line delimits the end of the HTTP request
    * So your HTTP Server program must read the first line from the TCP socket,
        parse out the <resource identifier> (e.g. /test.html) and then try to read
        that file (e.g. test.html) from your local file system (see TEST HTML FILES
        below).
      
  * <b>HTTP RESPONSE</b>:
    * The minimal response header is simply "HTTP/1.1 200 OK\n\n"  The backslash-n
              indicates a new line.  The extra new line means end of headers.  A typical
              server will send additional headers, but you don't need to.
    * After the response header and new line, you would follow with the content
          of the requested file.
    * You only need to handle GET requests (not POST, etc.)
    * Put a test html file in your IntelliJ project directory to make it easy to
          find.
    * You are probably familiar with the Scanner class which can be helpful with
          this task.  Otherwise you may prefer to use the FileReader class to open
          the file, and pass the FileReader to a BufferedReader to make the file
          easy to read a line at a time.
    * Repeatedly read a line from the file and write it to the socket, until the
              whole file has been read and sent.
    * Return the appropriate HTTP Status Code (404) if the file is not found.
    * You will visit your simple HTTP server using a browser.
              (You do *not* develop an HTTP client.)
    * A typical HTTP response header will have the content-length, or 
          "transfer-encoding chunked".  We are cheating and just closing the socket.
          This will indicate to the browser that the response has been completed.
<br>

##### TEST HTML FILES:
  * You must create your own test HTML file.  It can be as simple or complex
      as you like.  Use an existing html file if you have one.


##### Extra challenges (not required):
  * Return 405 Method Not Allowed if you receive an HTTP method you don't handle


##### Demonstrate your solution to a TA for credit.
  * Show the project, code, and execution in IntelliJ
  * You should demonstrate 200 OK and 404 File Not Found
  * If you don't complete the lab by the end of class, you must show a TA during
    their office hours before your next scheduled class on Monday or Tuesday.

