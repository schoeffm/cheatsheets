## Jenkins CI - Cheatsheet

### Grails + Geb + Headless UI Tests

#### System Prerequisites 
- First of all

        apt-get update

- Install Xvfb - an in-memory X11 Buffer. We'll start a session for every build-run and
  shut it down afterwards

        apt-get install xvfb gtk2-engines-pixbuf

- Install Fonts - so the browser does rendering right

        apt-get install xfonts-base xfonts-75dpi xfonts-100dpi xfonts-scalable xfonts-cyrillic

- Install Tools which come handy when debugging/checking the setup

        apt-get install x11-apps imagemagick

- Install a browser in order to run tests with (you can install whatever browser you need, but at
  least one should be available). We use Firefox in this example

        apt-get install firefox

Now everything should be in place to start testing - let's make a simple manuel check:

- Start Xvfb-Service with Display `:99` and Resolution 1280x1024

        Xvfb -ac :99 -screen 0 1280x1024x16

- Start a Firefox-Instance (let it render its chrome into Display `:99`)

        export DISPLAY=:99
        firefox http://www.google.de

- Take a Screenshot of the Display

        xwd -root -display :99 | convert xwd:- screenshot.png

#### Jenkins Prerequisites (Plugins)

We need a bunch of plugins in order to leverage the installed tools - so lets start.

- [Xvfb][xvfb] in order to start/stop X11 environments per job (and parametrize 'em). Just give your installation a name (to recognize it later). That should be enough as long as `xvfb` is in the PATH 
![System Settings for Xvfb-Plugin](assets/img/system_xvfb.png =650x)

- [Environment Injector Plugin][envInject] in order to set environment-variables for a specific build job (beware - they are environment-wide). There are no system-specific settings here, only job-related ones (we'll see later)

#### Creating the Job

Now since everything is setup - create an actual build-job and set it up like that:

- In the header of the job-configuration mark to remove old job-results
![Environment Varialbes set for this job](assets/img/job_environment.png =650x)

- Now the Xvfb-configuration - mark _Start xvfb ..._ and click _Advanced_ in order to set 
  more stuff. Since we've set the `DISPLAY`-variable to `:99` we use this as the 
  _display name_ to be used by Xvfb. Give it enough screen-space so everything you'd like to
  see fits the screen. Set the offset- and timeout-values as shown and that's it.
![Job Settings for Xvfb-Plugin](/jenkins/assets/img/job_xvfb.png?raw=true =650x)

- Now configure the Gails-Job itself. Since this is a vaadin-application with its own
  widgetset the first step is to compile that widgetset. Next compile everything else (the
  Java- and Groovy-stuff) and finally we'll execute __all__ tests (if you're only interested 
  in i.e. UI-tests then write `test-app functions:`)
![Grails build execution step](assets/img/job_grails.png =650x)

- Finally, although this is not a geb- or grails-specific step, you should publish the 
  test results (in order to get a nice overview about how many and what kind of tests failed
  during a run).
![Publish test results](assets/img/job_junit.png =650x)

Now that's it - run the job and see if tests fail.

### Buildpipelines

If you'd like to visualize a Build-Pipeline install the [respective plugin][buildPipe] and create a new View by clickin' the `+`-Sign at the tabbar.

![Create a new Pipeline-View](assets/img/build_pipe.png =300x)

Now, just enter a name, the job to start the pipeline (_other jobs will be determined 
automatically, as long as they are connected to the predecessor-job as their trigger_). For
all other configurations follow your instinct (and read the help-entries).

![Create a new Pipeline-View](assets/img/build_pipe_config.png =600x)


[xvfb]:https://wiki.jenkins-ci.org/display/JENKINS/Xvfb+Plugin
[envInject]:https://wiki.jenkins-ci.org/display/JENKINS/EnvInject+Plugin
[grails]:https://wiki.jenkins-ci.org/display/JENKINS/Grails+Plugin
[junit]:https://wiki.jenkins-ci.org/display/JENKINS/JUnit+Plugin
[buildPipe]:https://wiki.jenkins-ci.org/display/JENKINS/Build+Pipeline+Plugin
