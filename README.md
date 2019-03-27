# Conveyal Analysis

This is the server component of [Conveyal Analysis](http://conveyal.com/analysis), which allows users to create public 
transport scenarios and evaluate them in terms of accessibility.

**Please note:** At this time Conveyal does not provide support for third-party deployments of Analysis. We provide paid 
subscriptions to a hosted deployment of this system, as well as transportation planning consulting for subscribers.

For now, the project is open source primarily to prevent vendor lock-in for our clients and to ensure transparency in 
planning and decision making processes. It is likely that over time the system will become easier to deploy by third 
parties, but we do not plan to provide technical support for such deployments.

**Note**: This modified version would look for a GTFS package in the main directory (analysis-backend/gtfs.zip) instead of getting the GTFS package from user upload. This is to prepare for an integration between [Retro-GTFS](https://github.com/CUTR-at-USF/retro-gtfs/tree/GTFS-Realtime) and Conveyal Analysis where Retro-GTFS results, which are saved on disk, would be loaded automatically to Conveyal Analysis.

## Configuration

Conveyal Analysis can be run locally (e.g. on your laptop) or on Amazon Web Services EC2 instances, depending on the 
configuration set in analysis.properties.  With extensions requiring software development skills, it could be modified 
to run in other cloud-computing environments.
                                           
To get started, copy the template configuration (`analysis.properties.tmp`) to `analysis.properties`.  

To run locally, use the default values in the template configuration file. `offline = true` will create a local instance 
that avoids cloud-based storage, database, or authentication services.  To run regional accessibility analyses (as 
opposed to single-point isochrone analyses), you will need to set up an AWS S3 bucket and set the value of `results_bucket`.
By default, analysis-backend will use the `analysis` database in a local MongoDB instance, so you'll also need to 
install and start a MongoDB instance.

To run on the cloud, we use Auth0 for authentication and S3 for storage; configure these services as needed, then set 
the corresponding variables including:

- `auth0-client-id`: your Auth0 client ID
- `auth0-secret`: your Auth0 client secret
- `database-uri`: URI to your Mongo cluster
- `database-name`: name of project database in your Mongo cluster
- `frontend-url`: URL of the analysis-ui frontend (see below)
- `bundle_bucket`: S3 bucket for storing GTFS bundles and built transport networks
- `grid_bucket`: S3 bucket for storing opportunity dataset grids
- `results_bucket`: S3 bucket for storing regional analysis results

You will need S3 credentials set up in your environment or in `~/.aws` for an identity that is allowed to access all the 
buckets above. If you have multiple profiles, you can use the `AWS_PROFILE` variable in the environment to choose which 
AWS credentials profile will be used.

## Building and running

Once you have configured `analysis.properties` and started mongo locally, build the application with `mvn package` and 
start it with `java -Xmx2g -jar target/analysis.jar`

Next, follow the instructions to start the [analysis-ui frontend](https://github.com/conveyal/analysis-ui) . Once that 
is running, you should be able to log in without authentication (using the frontend URL, e.g. http://localhost:9966). 
Note that the default value of the analysis-backend `frontend-url` variable is a prebuilt copy of the frontend that 
relies on Conveyal's authentication setup; in general this will not work with local installations, so this value should 
be ignored.

## Creating a development environment

In order to do development on the frontend, backend, or on [R5](https://github.com/conveyal/r5), which we use for
performing the analyses, you'll want a local development environment. We use [IntelliJ IDEA](https://www.jetbrains.com/idea/)
(free/community version is fine) and add analysis-backend as a new project from existing sources. We also typically clone
R5 with `git`, then use the green plus button in the Maven panel to add R5 as a Maven Project within the same IntelliJ project. 
Check to make sure that the version of R5 matches the version specified in the analysis-backend `pom.xml`.  

You can then create a run configuration for `com.conveyal.taui.AnalysisServer`, which is the main class. You will need to
configure the options mentioned above.
