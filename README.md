# Programming Contest

A programming contest initiated by the mods at [/r/shittyprogramming](https://www.reddit.com/r/shittyprogramming/comments/8t2j1l/shittyprogramming_deathmatch_is_tonight_8pm_est/).
The idea is to implement the best solution for a specification we're given in the shortest possible time frame.
Neither me nor [my opponent](https://github.com/fascinatedBox/) knows what we're supposed to implement,
and the contest will last for some 2-3 hours, streamed in front of a live audience.

[Read more about its details here](https://gaiasoul.com/2018/06/22/shitty-programming-death-match-tonight/).

## What I did

After 3 hours and 23 minutes, I had the following features.

1. Login, logout, securely.
2. Backend administration system, for creating and deleting (not editing) new _"exhibits"_.
3. Front end part (for guest visitors and _"kiosks"_) allowing a user to view exhibits and purchase tickets to exhibits.
4. PayPal integration for purchasing tickets.

The system used a real MySQL database, and real (and secure) logins, with the ability to create
multiple administrator accounts, and finegrained access control to the system - Allowing an admin
user to _"administrate"_ it easily, creating new _"exhibits"_, and delete existing exhibits.
Of course, what you can do in 3 hours is limited, but I don't think my results was below pair
in any ways.

I think it was sporty of [FascinatedBox](https://github.com/fascinatedBox/) to show up, and I
think we both had a nice time doing this :)

### What a guest sees when he visits the URL of the system.

![alt screenshot 1](https://phosphorusfive.files.wordpress.com/2018/06/screen-shot-2018-06-23-at-06-22-48.png)

### What a guest sees when he clicks the _"exhibit"_

![alt screenshot 2](https://phosphorusfive.files.wordpress.com/2018/06/screen-shot-2018-06-23-at-06-27-13.png)

### When a guest clicks a specific _"exhibit"_

![alt screenshot 3](https://phosphorusfive.files.wordpress.com/2018/06/screen-shot-2018-06-23-at-06-27-21.png)

Notice, the guest can immediately purchase a ticket, which will use PayPal's API to placean
actual order, and allow the guest to pay using his PayPal account.

### Ourchasing a ticket

![alt screenshot 4](https://phosphorusfive.files.wordpress.com/2018/06/screen-shot-2018-06-23-at-06-27-31.png)

Notice, the above is actual PayPal integration, and the system is already ready to accept payments.
When the user has purchased a ticket, he needs to take a photo of the screen, which will be his _"ticket"_,
which he can show to the guy in the desk accepting tickets. When a _"ticket"_ is purchased, an
actual record will be inserted into the MySQL database table called _"ticket"_. Below is the entire
create schema for the database.

If I had had another hour, I'd probably be able to send an email to the guest, with an actual ticket,
instead of this silly _"take a photo of your ticket"_ thingie ...

**MySQL schema for the system's database**

```sql
CREATE DATABASE `ingen` /*!40100 DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci */

CREATE TABLE `exhibit` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `name` varchar(512) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `when` datetime NOT NULL,
  `price` int(10) unsigned NOT NULL,
  `description` longtext CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci

CREATE TABLE `ticket` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `ticket_unique_id` varchar(100) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci DEFAULT NULL,
  `exhibit_id` int(10) unsigned NOT NULL,
  `used` bit(1) NOT NULL,
  PRIMARY KEY (`id`),
  KEY `exhibit_id` (`exhibit_id`),
  CONSTRAINT `ticket_ibfk_1` FOREIGN KEY (`exhibit_id`) REFERENCES `exhibit` (`id`) ON DELETE CASCADE
) ENGINE=InnoDB AUTO_INCREMENT=3 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci

```

The system will also create its own database when it is initially installed on a server, facilitating
for a _"zero configuration"_ type of system.

### The admin backend

The system determines if you're an admin or just a visitor based upon whether or not you're logged
in or not. The entire system then loads it forms dynamically, using Ajax. If you're not loggedin,
you can click the _"key"_ button to login. Below is how it looks like when an admin visits the
system.

![alt screenshot 5](https://phosphorusfive.files.wordpress.com/2018/06/screen-shot-2018-06-23-at-06-23-10.png)

The above is how it looks if the user is logged in, and hence are accessing the _"backend"_ system.

### Admin editing exhibits

![alt screenshot 6](https://phosphorusfive.files.wordpress.com/2018/06/screen-shot-2018-06-23-at-06-24-22.png)

The above is a datagrid dynamically databound towards the MySQL _"ingen"_ database, and its table
called _"exhibit"_. I implemented deletion and adding new exhibits, but didn't have the time to
implement editing of records.

### Adding a new exhibit

![alt screenshot 7](https://phosphorusfive.files.wordpress.com/2018/06/screen-shot-2018-06-23-at-06-26-18.png)

The above is how it looks like when the (admin) user is creating new exhibits.

### Logging in

![alt screenshot 8](https://phosphorusfive.files.wordpress.com/2018/06/screen-shot-2018-06-23-at-06-23-00.png)

The above is how it looks like when a user is logging into the system. This will use a privately and
highly secured password file, from inside of it will use a Blowfish based (_slow hashing_) to store
the passwords. The system also has authorisation features, and allows the admin user to control
access to it.

The user is persisted in a cookie, which is only accessible over HTTP, and not from JavaScript. In
addition the system automatically sets itself up using SSL, creating a new Let's Encrypt SSL keypair
during installation, so it should be 100% bullet proof in regards to security in all ways.

### Skinning the system

By changing the skin file used to any of the pre-defined skins from Micro, you can
completely change its appearance. Below is how it looks like when using the _"magic-forrest"_
skin.

![alt green skin](https://phosphorusfive.files.wordpress.com/2018/06/screen-shot-2018-06-23-at-07-02-23.png)

### Test the system as it was after 3 hours of coding

At the end of the contest, I uploaded the system below, such that you can try out its front end.

I want to emphasize that I literally spent **3 hours implementing this**, and was quite often
interrupted too to speak with CalSosta, the admin and moderator of the whole contest.

* [Try out the system's front end](https://home.gaiasoul.com/programming-contest)

The _"Kiosk"_ parts of the system I was not even able to seriously start on. However, I kind of
did a wrong design decision early on, trying to separate these into two distinct parts, which
they shouldn't have been.

Below is the specification we were given, which is basically to implement a better datasystem for
a Jurassic park kind of theme park, where security was key.

### It was for fun ;)

Although I must confess I am highly competetive of nature, this was something we did for
**fun** - And I think FascinatedBox was a good sport to join in, and I believe we both had
a lot of fun!

## Specification

Introduction

### Purpose

This document describes requirements for a system to manage the day to day operations of a new InGen
project to create a theme park located on the island of Isla Nubar.  InGen prides itself on creating
unimaginable experiences for our guests and this should be reflected in our own management systems
internally as well as those which face our customers.

### Scope

InGen seeks to build a singular web based application with multiple views into the various sub-systems
which run the operations of the park.  These will include consoles for our security, operations and
research teams internally as well as ticket management and exhibit kiosks for our guests.

The system is mission critical and must be fast as well as secure.  Previous Unix based systems had
to be scrapped entirely due to massive security flaws in the architecture of the system.  The new
system must meet the strictest security concerns.

Most importantly the system will need to be well designed, look beautiful and delight guests of all ages.

### Description

#### Features

The system will be comprised of web based interfaces for Guests and Internal Employees.
Guest Facing systems will consist Kiosks which can run the Ticketing and Exhibit Information
applications.

Internal systems will have an interface that allow InGen Employees to view applications for Security,
Operations and Research.

#### System Features

#### Core System

The core system should allow employees to log in and select an internal application or run a kiosk.

#### Security

The security system should show the real-time status of security sub-systems and allow for management
of the systems where applicable.

#### Animal Paddocks

The Animal Paddock sub-system should allow for the monitoring and management of several animal
paddocks located throughout the island.

Animal Paddocks should include fields for:

* __Paddock Energy Status__ - To indicate if the Paddock is energized and working
* __Paddock Location__ - To show the physical location of the paddock on the island
* __Paddock Criticality__ - To show the criticality and potential danger of a failure

Animal Paddocks should include actions for:

* Enabling/Disable Paddock Energy
* Alerting the User if a _"Level 1"_ Criticality Paddock becomes de-energized unexpectedly

Animal Paddocks should show:

* A list of all entries
* A form to edit entries and fields
* A map of all paddocks and their energy states

#### Door Locks

The Door Locks sub system should show secure doors status as well as being able to remotely lock
and unlock the door.

Door Locks should include fields for:

* __Door location__ - To show the physical location of the door
* __Door Lock Status__ - To indicate if the door is locked or unlocked
* __Door Ajarness Level__ - To display the level of openess of the door.

Door Locks should include actions for:

* Remotely locking and unlocking the door
* Alerting the user if all doors become unlocked

Door locks display should contain views for:

* A list of all entries and fields
* A form to edit entries and fields
* A map displaying doors and their states

#### Security Cameras

The security cameras sub-system should show locations and image feeds of security cameras.

Security Cameras should include fields for:

* __Camera Location__ - To show the physical location of the camera

Security Cameras should contain views for:

* A list of all entries and fields
* A form to edit entries and fields
* An image based stream of the camera source

#### Operations

The operations sub-system should show a console that allows employees to monitor for potential
issues in park operations.

#### Weather

The weather sub-system should show an interface of current and predicted weather around Isla Nubar.
No information will be stored for this sub-system.

Weather should contain views for:

* A real time feed of weather information sourced from an external vendor.

#### Vehicle Tours

The park will employ a number of automated vehicles for tours of the island. The Vehicle Tours
system should manage and track these vehicles.

Vehicle Tours should include fields for:

* __Vehicle Information__ - Name, Make, Model and other fields to identify the vehicle
* __Vehicle Maintenance Status__ - To show whether the vehicle is active, reporting for maintenance on being maintained.
* __GPS Location__ - To show the real-time physical location of the vehicle on the island
* __Speed__ - To Track Vehicle Speed
* __Door Ajarness__ - To show if a door is open

Vehicle Tours should include actions for:

* Starting and stopping a vehicle tour
* Recalling a vehicle for maintenance

Vehicle Tours should contain views for:
* A list of all entries and fields
* A form to edit entries and fields

#### Research

Research systems should show information about experiments and lab procedures as well as relevant
information from associated IoT sensors.  These may include thermometers, gyroscopic, or other
biological sensors

#### Experiments

Experiments will show a list of experiments, as well as real-time information from sensors.

Experiments should include fields for:

* __Experiment information__ - Including Researcher names, experiment description, expected results, etc.
* __Sensor list__ - A list of IoT sensors and their end-point information

Experiments should contain views for:

* A list of all entries and fields
* A form to edit entries and fields
* An enhanced form view to show sensors and their information

#### Guest Systems

Guest Systems include all systems to manage systems used by guests as well as interfaces for the
guests themselves.

#### Ticketing

Ticketing will allow guests to purchase passes for exhibits as well as allow internal employees to
manage exhibits.

Ticketing should include fields for:

* __Exhibit information__ - To contain information about the various exhibits
* __Exhibit cost__ - The amount of money it costs
* __Exhibit times__

Ticketing should include actions for:

* Purchasing a quantity of tickets

Ticketing should contain views for:

* (Internal Only) A list of all entries and fields
* (Internal Only) A form to edit entries and fields
* Browsing exhibits and purchasing tickets (CRYPTO ONLY)

#### Exhibit Kiosks

Exhibit Kiosks are intended to provide information to guests for exhibits.  These kiosks are
available in the museum and walking portions of the part, NOT FOR ANIMAL PADDOCKS.  The system
will also allow internal employees to manage information for the kiosk.

Exhibit Kiosks should include fields for:

* __Kiosk Information__ - Including animal or display name, details

Exhibit Kiosks should contain views for:

* A list of all entries and fields
* A form to edit entries and fields
* Displaying static or interactive data on the kiosk

External Interfaces
Security Camera Feeds
Vehicle GPS
Laboratory Temperature Sensors
Weather
Paddock Fence

Non-functional Requirements

Appendix
Maps
