# Set Stage - what's are we doing today
# What is Habitat
Chef Habitat provides automation capabilities for defining, packaging and delivering applications to almost any environment regardless of operating system or deployment platform. The core components of Chef Habitat that we will be touching on in this guide include:

## slides
### Core Components
Chef Habitat Studio
Chef Habitat Artifact
Chef Habitat Supervisor

### Chef Habitat Studio
### Chef Habitat Artifact 
### Chef Habitat Supervisor



# SQL info and hooks
As Microsoft SQL is a commercial off the shelf (COTS) software and does not require us to build the application first this
guide will mostly deal with how to program the Habitat Supervisor using the built in hooks provided in Chef Habitat.
Hooks are used to model deployment tasks as code that can be configured dynamically and executed automatically.
Once instructions are defined via a Hook the Chef Habitat Supervisor acts as a workflow engine, coordinating the flow of
information between services, and automatically sequencing the events defined in their plans.
Current available hooks include:
• file-updated
• health-check
2
• init
• reload
• reconfigure
• suitability
• run
• post-run
• post-stop
In addition to providing the means to codify deployment tasks as part of an application artifact, these hooks can make
use of Chef Habitat’s internal runtime settings. These include parameterized references to system resources (e.g. IP
addresses, hostnames), application metadata (e.g. version, dependencies), helper functions (e.g. cluster elections, health
status), and user-defined variables & tunables

# Considerations
Microsoft SQL Server has a few characteristics that make it more difficult to package and run than other commercial off
the shelf software (COTS).
The SQL Server binaries (like sqlservr.exe and its supporting libraries) cannot simply be extracted and moved to /hab/
pkgs/. SQL Server makes extensive use of the Windows registry which tracks the location on disk of all SQL Server
“instances” along with much of their service metadata. Also, the absolute paths to the data files of the model and msdb
system databases are stored in the master database. So in order to reliably install SQL Server in working condition, you
need to do so via its installer binaries. Unfortunately this also means you may need to package the installer binaries which
weigh in at about 1.5 GB in SQL Server 2017.
Another challenge is that your Chef Habitat run hook cannot simply call sqlservr.exe. On the surface it seems like that
should work since that is what the Windows service invokes and if you run that from a shell, it successfully starts and runs
the database engine. However, sqlservr does not handle ctrl+c signals gracefully, which is how Chef Habitat stops
running services by default. The SQL Server engine will prompt you for confirmation to terminate the database and to my
knowledge that cannot be suppressed. We could forcibly terminate the process, but that may leave the database in an
unrecoverable state. So we do need to have our hooks interact with the SQL Server Windows service.
All of these challenges can be dealt with so let’s jump in and explore a working SQL Server Chef Habitat plan. I’ll be
including the relevant sample Chef Habitat plan and hook code in this post but please see the core-plans repository for a
complete Chef Habitat plan.

# Getting Started

# Installing

# Setting Ports and Opening Firewall

# Running the SQL Server Service

# Setting up Logins and Users

# Exporting SQL Server to a Docker Image

# Testing Connectivity to a “Habitized” SQL Server

# Using a Habitat Update strategy to patch SQL Server