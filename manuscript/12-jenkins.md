# Jenkins

TODO: Put to the presentation

It all started by a visionary developer, Kohsuke Kawaguchi, in 2004 while he was working for Sun Microsystems. He had a hobby project he named Hudson. The goal was to create a platform that can be used to execute scheduled tasks. Over the years, Hudson evolved and was adopted by an ever increasing number of teams within Sun. Around 2008 the value behind Hudson was widely recognized and Kohsuke started working full time on its development and support. *Around 2010 it was clear that Hudson was the leading solution for continuous integration. It owned around 70% of the market.*

2009 marks the beginning of the end of Hudson. Oracle purchased Sun and tensions arose between Hudson's developer community and Oracle. The community wanted to keep the project open while Oracle claimed Hudson's trademark. Oracle wanted more control over the development process which was in stark contrast with community (led by Kohsuke) goal to keeping it open. The result of the conflict was Hudson's fork called Jenkins. Hudson ended up under Oracle's umbrella while Jenkins continued being an open source project. Today, *Hudson hardly even exists while Jenkins continues being undisputed market leader in continuous integration as well as continuous delivery and deployment.*

Platform based on plugins ecosystem allowed Jenkins to strive. The community grew at a rapid pace converting Jenkins into one of the biggest open source projects. Today, with more than 1200 plugins, there is hardly anything we might need to do that is not covered. Need to checkout the code from your GitHub repository? There is a plugin (or two) just for that. Need to build your project with Gradle? Simply choose one of the Gradle plugins that suits your needs.

So, what is Jenkins? The answer to that question comes in two parts. To understand Jenkins today, we need to understand how it was yesterday.

## The World Before Jenkins Pipeline

TODO: Put to the presentation

In a nutshell, *Jenkins allows us to schedule jobs*. We can schedule *a time, a frequency, or external triggers* that will execute a predefined set of tasks. Initially, a job would be a single task that, for example, builds an application, runs tests, or performs software deployment. A complex process would consists of *a set of interconnected jobs*. The first job would trigger the second, the second would trigger the third, all the way until the whole process is executed. The definition of those jobs would be created using Jenkins' UI. We would create a new job, select a plugin that, for example, checks out the code from our repository and fill in a few fields. As a result, the job would check out the code when Jenkins detects a new commit to the repository.

Time goes on and what was good yesterday is not necessarily just as good today. Jenkins' ability to create and connect as many jobs into a single process and its reliance on UI are some of the features that made it so popular. However, those same features may not fulfil the needs the software industry has today. In general, developer tools are moving towards using code for everything. For an engineer, it is easier to specify through code what and when something should happen, than using an UI. Code can be versioned and reviewed. It allows us to be more precise in describing our expectations. Finally, a code will be able to define in a relatively easy way a set of complex operations that would be quite challenging to create through UI. Further on, with the move towards self-sufficient autonomous teams, came the need to be able to define jobs in a decentralized manner. We needed a way to store jobs related to an application in the same repository where its code is. Those needs are in stark contrast with Jenkins which assumes that everything is defined in one central location and controlled by its UI.

Processes we're trying to automate today are often much more complex than those we aimed for in the past. While, before, we would be happy having Jenkins check out the code, run some tests, and build the application, now we need it to run vastly bigger amounts of tests in parallel, deploy to production, and even be triggered by our monitoring system when things go wrong. As the result, the average number of interconnected jobs increased and, with them, the complexity to define and maintain them. We needed a unique view for the whole process.

### The Need for Change

TODO: Put to the presentation

Over time, Jenkins, like most other self-hosted CI/CD tools, tends to *accumulate a vast number of jobs*. Having a lot of them causes quite an increase in maintenance cost. Maintaining ten jobs is easy. It becomes a bit harder (but still bearable) to manage a hundred. When the number of jobs increases to hundreds or even thousands, *managing them becomes very tedious and time demanding*.

Imagine that we need to change all those jobs from, let’s say, Maven to Gradle. We can choose to start modifying them through the Jenkins UI, but that takes too much time. We can apply changes directly to Jenkins XML files that represent those jobs but that is too complicated and error prone. There are quite a few plugins that can help us to apply changes to multiple jobs at once, but none of them is truly successful (at least among free plugins). They all suffer from one deficiency or another. The problem is not whether we have the tools to perform massive changes to our jobs, but whether jobs are defined in a way that they can be easily maintained.

Besides the sheer number of Jenkins jobs, *another critical Jenkins’ pain point is centralization*. While having everything in one location provides a lot of benefits (visibility, reporting and so on), it also poses quite a few difficulties. Since the emergence of agile and, especially, DevOps processes, there’s been a massive movement towards self-sufficient teams. Instead of horizontal organization with separate development, testing, infrastructure, operations, and other groups, more and more companies are moving (or already moved) towards teams organized vertically. As a result, having one centralized place that defines all the CD flows becomes a liability and often impedes us from splitting teams vertically based on projects. Members of a team should be able to collaborate effectively without too much reliance on other teams or departments. Translated to CD needs, that means that each team should be able to define the deployment flow of the application they are developing.

Finally, Jenkins, like many other tools, is *primarily based on its UI*. While that is welcome and needed as a way to get a visual overview through dashboards and reports, it is suboptimal as a way to define the delivery and deployment flow. Jenkins originated in an era when it was fashionable to use UIs for everything. If you worked in this industry long enough, you probably saw the swarm of tools that rely entirely on UIs, drag & drop operations, and a lot of forms that should be filled. As a result, we got products that produce artifacts that cannot be easily stored in a code repository and are hard to reason with when anything but simple operations are to be performed. Things changed since then, and now we know that many things (deployment flow being one of them) are much easier to express through code. That can be observed when, for example, we try to define a complex flow through many Jenkins jobs. When deployment complexity requires conditional executions and some kind of a simple intelligence that depends on results of different steps, chained jobs are indeed complicated and, often, impossible to create.

Luckily, all those, and many other deficiencies are now a thing of the past. With the emergence of the *Pipeline Plugin* and many others that were created on top of it, Jenkins entered a new era and proved itself as a dominant player in the CI/CD market. A whole new ecosystem was born, and the door was opened for very exciting possibilities in the future.

What *Jenkins 2.0* brings to the table is the obvious movement towards CD expressed as code. It's not doing it using the big-bang approach. Instead, it chose to maintain backward compatibility with a gentle push towards the future. Due to its enormous market share, it would be unrealistic to expect that all its users should drop years of investment and start fresh. What we see through the new release is a door that just opened and is waiting for you to step through at your pace. That door is the *Pipeline*. Until now, the *Pipeline* was just another plugin. Starting from the *version 2.0*, it is the first class citizen that will define the way we are creating future CD flows.

## Continuous Delivery or Deployment Flow with Jenkins

TODO: Put to the presentation

In all but small projects, tasks that constitute the flow are anything but straightforward and linear. The deployment flow does not consist only of building, testing, and deployment nor it is always defined in a linear fashion.

In many instances, the process is far more complicated. There are many tasks to run, and each of them might produce a failure. In some cases, a failure should only stop the process, while, in others, some additional logic should be executed as part of the after-failure cleanup. We might need to revert to the previous release, rollback the proxy, de-register the service, and so on. Some tasks are run in one of the testing servers while others are run on the production cluster. Some parts of the flow are not linear and depend on task results. Some tasks should be executed in parallel to improve the overall time required to run them. The list goes on and on. The complexity can be, often, very high and cannot be solved by simple chaining of Freestyle jobs. Even in cases when such chaining is possible, the maintenance cost tends to be very high.

On top of that complexity, add conditional logic. In many cases, it is not enough to chain jobs in a linear fashion. Often, we do not want only to create a job that, once it’s finished running, executes some other job. In real-world situations, things are more complicated than that. We might need to run some tasks, and, depending on the result, invoke some other jobs, then run some tasks in parallel, and, finally, execute some jobs only when all others were finished successfully. Those things are easy to accomplish through code while chained Jenkins jobs, created through the UI, pose difficulties to create even a simple conditional logic.

All those needs, and many others, had to be addressed in Jenkins if it was to continue being a dominant CI/CD tool. Fortunately, developers behind the project understood those needs and, as the result, the Jenkins Pipeline become the first class citizen. The future of Jenkins lies in a transition from Freestlyle chained jobs to a single pipeline expressed as code. Modern delivery flows cannot be expressed and easily maintained through UI drag and drop features, nor through chained jobs. They can neither be defined through YML (Yet Another Markup Language) definitions proposed by some of the newer tools (which I’m not going to name). We need to go back to code as a primary way to define not only the applications and services we are developing but almost everything else.

## What is Jenkins Pipeline?

TODO: Put to the presentation

TODO: Write

## The Challenges When Transitioning From FreeStyle Jobs Towards Jenkins Pipeline

TODO: Put to the presentation

The change introduced with the Jenkins Pipeline is much bigger than what would appear on the first look. It, in a way, changed the core principles behind the original Jenkins design. The UI as the principal driver behind job definitions was changed for code. Jobs that were defined in a central place are now distributed inside different code repositories and defined as a simple Jenkinsfile file. Many interconnected jobs can now be defined as a single job that represents the whole process with all the tasks. UI with heavy centralization with the concept of many interconnected jobs are now replaced with decentralized code that defines the whole flow as a single file. What was in the past maintained by a dedicated team is now distributed among many teams that do not necessarily even see the Jenkins UI.

Such a big change is often confusing to Jenkins old timers. People who used Jenkins for years are accustomed to a certain way of working. When they are presented with such a drastic change, the initial reaction is often rejection. Jenkins fomented learning that led people into one direction. With the emergence of the Pipeline, the direction changed. While coding skills were not necessary in the past, now a person needs to have at least basic coding knowledge. That does not mean that one needs to be an expert coder. However, it also does not mean that coding skills are not required. They are welcome and provide a quantitative improvement for the whole organization.

Control is another big change. While it is certainly possible to establish the same, or even higher level of control with Jenkins Pipeline, it foments distributed job maintenance. If a team in charge of a service is in charge of its CD flow, the control, of better said, responsibility for that flow is moved from a Jenkins dedicated team to the whole organization. In the past, the team working on a service would need to have access to the shared Jenkins master to make modifications to their jobs or they'd need to send a request to the Jenkins maintenance team. Now, with the emergence of Jenkins file, all they'd need to do is modify the flow defined inside it and commit the change to the repository. Such a change in the way jobs are defined often needs reorganization of skills and roles within a company. With Jenkinsfile, teams are closer to the objective of being self-sufficient. The can not only work on the code of their services, but also control the flow the service has after a commit. They can define which tests are run and when, how their code should be built, when it should be deployed to the staging environment and when to production. Those changes are not mandatory. Pipeline based jobs can still be created inside Jenkins and an organization can continue with a similar set of roles and responsabilities they had before. That is a common practice when taken as a transition towards decentralization through Jenkinsfile.

Finally, the Pipeline encourages teams to define the whole CD flow as a single job. The FreeStyle jobs that were the norm in the past went hand in hand with the traditional organizational structure. Development team would create a job or a set of jobs that fulfil their needs. The final job in the chain would be hooked into another group of jobs belonging to, for example, the testing department. In turn, their jobs would flow into a third group of jobs owned by operations. And so on, and so forth. Each department would have one or more jobs they own. With the Pipeline, the whole flow can be defined as a single job. Such an approach has a lot of benefits. For one, there is a unique view of the readiness of the software that passes through the pipeline. Transition between different tasks is more natural and easier to accomplish. Finally, a unique pipeline represents a single service and, from the logical point of view, removes, often unnatural, barriers posed by separate departments. The price to pay is the need to rethink not only the deployment strategy but often to reorganize the structure of the organization we're working in. Pipeline allows us to put the word "continuous" in front of delivery. It removes the barrier we had before. However, for those barriers to truly disappear, technical solution needs to be accompanied with cultural change. The organization we work in needs to become more flexible and put more trust into their teams.

Coding combined with decentralization and the need to restructure the organization we work in can seem daunting at first. However, the effort required as investment in change pays of quickly and allows us to get closer to the goal commonly called continuous delivery or deployment. It allows us to be faster and more reliable at the same time.

TODO: What is Jenkins Pipeline

## The Difference Between Jenkins OSS and Enterprise

TODO: Put to the presentation

TODO: Write

## Basic Features

TODO: Put to the presentation

TODO: Write

## Top CJP Features

TODO: Put to the presentation

TODO: Write

## Sizing and Pricing

TODO: Put to the presentation

TODO: Write

