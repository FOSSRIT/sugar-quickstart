
# Sugar_Activity_Quickstart

This is the framework for a Sugar Activity. Everything needed to quickly start an activity for the Sugar OLPC XO. Credit for this repo is given to Robin Brooke, whose blog can be found [here: http://rbrooke.blogspot.com/2010/01/creating-xo-file.html](http://rbrooke.blogspot.com/2010/01/creating-xo-file.html)

**NOTE: The latest Sugar (since the Gtk3 migration) now ignores MANIFEST files, don't create one.**

## Table of Contents

- File Hierarchy
    - What is included
    - Description of Project Files
- Building & Distributing
- GTK and Interfaces
- Multi-Lingual Development
- Example Activity


## File Hierarchy

A basic bare-minimum Sugar Activity will consist of these files:

- activity/
    - activity.info
    - activity-icon.svg
- activity.py
- setup.py

_This activity does not include an `activity-icon.svg`.  You must build (or extend) the `activity.py` file.  Changes to the `activity.py` file must be accomodated by changes to the `activity.info`.  You will use the `setup.py` file to create a `.xo` file later._


### Included in This QuickStart

- /activity
    - activity.info
- activity.py
- setup.py
- README.md (this file)
- README.html


### Description of Project Files

As you would expect, each file serves a specific purpose.

The `activity` folder stores meta-data about the activity.  This includes an `activity.info` file which describes the activity and how to execute it from the sugar desktop.  It also stores the activity display icon in SVG (XML Standard Vector Graphics) format.

The `setup.py` file aids with testing, building, and installing activities.  It does not change between activities.  It will help generate language files, symlink to your sugar activities for development.  It also packages the software into a compressed `.xo` file for distribution to other XO laptops.

A commonly used name is `activity.py`, but you can technically call it whatever you like.  This is the name used most commonly by sugar activities.


---

#### setup.py


This file is used to generate your po translation files, build/test your code, symlink it for development, and creating a compressed .xo package file.

If you run it with no commands it will give you all available commands and a short description.

_It must be executable to be run._

It is two lines long:

    from sugar3.activity import bundlebuilder
    bundlebuilder.start()

Example Commands:

    ./setup.py genpot
    ./setup.py build
    ./setup.py dev
    ./setup.py dist_xo


#### activity/

This folder contains your `activity.info` file used to launch your activity, as well as your SVG icon.


#### activity/activity.info

This file is used to define the icon used on the sugar desktop, and is responsible for executing the program when that icon is selected.

Here is an example (included in the default files):

    [Activity]
    name = example
    bundle_id = example.laptop.org
    icon = exampleicon
    exec = sugar-activity activity.Example
    show_launcher = yes
    activity_version = 1
    license = GPLv2+

Now to explain what each of these do:

- name
    - The name of the activity as seen by the user.
- bundle_id
    - Unique name used by sugar to refer to your activity, which may also be used in your code (such as accessing the journal).
- icon
    - The name of your SVG icon (automatically infers a `.svg` suffix, **do not include .svg**)
- exec
    - Used to launch your activity, specifying the type (`sugar-activity`), the file name, and the method to use to launch your program.
- show_launcher
    - Display the activity in the sugar activity panel
- activity_version
    - An integer representing the iteration of your activity (_cannot have decimals_)
- license
    - The license your software is under, explains to users their rights with regard to your software.
    - GPL is a popular license, both 2 and 3 are commonly used.
        - GPLv2+ means version 2 and newer applies.

Troubleshooting:

- If the icon fails to display, it is likely that the icon name in `activity.info` is invalid.
    - Remember not to include the `.svg` suffix for the file.
- If you are working on an older version of an activity, make sure the correct file and class names are used by `exec`.


#### activity/activity.svg

The icons are in SVG format (XML Standard Vector Graphics).  It is suggested that users create icons using [Inkscape](http://inkscape.org/), a wonderful free and open source vector graphics software.

**Sugar recommends a 48x48 pixel size for its icons.**

After creating an icon in Inkscape, some fine-tuning is advised:

**Part One:**

    Before:--------------------------------
    <?xml version="1.0" encoding="UTF-8" standalone="no"?>
    <!-- Created with Inkscape (http://www.inkscape.org/) -->

    After:---------------------------------
    <?xml version="1.0" ?><!DOCTYPE svg PUBLIC '-//W3C//DTD SVG 1.1//EN' 'http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd'
    [<!ENTITY stroke_color "#000000">
    <!ENTITY fill_color "#FFFFFF">]><svg

**Part TWO:**

    Before:--------------------------------
    <rect
    style="fill:#ffffff;stroke:#000000;stroke-opacity:1"
    id="rect904"
    width="36.142857"
    height="32.142857"
    x="4.1428571"
    y="7.1428571" />

    After:---------------------------------
    <rect
    style="fill:&fill_color;;stroke:&stroke_color;;stroke-opacity:1"
    id="rect904"
    width="36.142857"
    height="32.142857"
    x="4.1428571"
    y="7.1428571" />

Saving these changes will set sugar colors for FILL and STROKE within the STYLE attribute.


#### activity.py

Activities are programmed in the [python programming language](http://www.python.org/).

Activities extend the sugar library, which are classes, and therefore need to be designed around class structures.  Without extending the sugar components they will not be executed properly under the sugar environment.

_Larger programs can be separated into multiple files and included as components._

**Known Bug: Classes extending sugar components will not work outside the sugar desktop environment.**

There are a few options for developing for, or testing outside the sugar desktop environment:

1. Create a separate version of the software that does not extend sugar library, and merging changes to the sugar version once tests are successful.
2. Create two versions of the top-level class and shared classes in separate files, executing one in sugar and the other for testing.


## Building & Distributing

There are two ways to install and test your software.  Both assume that an XO laptop or Sugar Desktop Environment (or Development Environment) are being used.

1. Create a `.xo` file and distribute it to the XO laptop(s)

To create a package run:

    ./setup genpot
    ./setup build
    ./setup dist_xo

The last command will generate a `dist/` folder with a `.xo` file inside it named after your activity.  This file is a compressed Sugar Package file which can be installed off a USB drive to other XO laptops.


2. Create a symlink to the development activity using `./setup.py dev`

A symlink only has to be created once, but you should run these two commands first (and preferably before each test run):

    ./setup genpot
    ./setup build

**If your activity does not appear on the desktop after you have installed it, check the activity list, and check your `activity.info` file for invalid values.**


## GTK and Interfaces

Sugar has migrated to Gtk3, which uses GObject Introspection.

While this sounds complex, what it means is that the new library of code describes itself, and is used to automatically generate 100% accurate documentation.  Unfortunately, this documentation is currently only written in C, so you may have to read C documentation to build with python Gtk3.

This can be a bit tricky, but hopefully the tips here will help new developers going forward.

First, sugar has changed the Gtk3 compatible import to sugar3, which means imports from the sugar libraries should have the sugar3 prefix.

Second, all of the GObject libraries are imported through the `gi.repository` library.

_Note: The sugar3.activity.Activity extends Gtk.Window, which means if you want to test a software outside sugar you can (mostly) swap in Gtk.Window where the class extends sugar3.activity.Activity._


## Multi-Lingual Development

When you run `./setup.py genpot` this parses your software for any strings wrapped with gettext, and generates a `.po` file.  This `.po` file can then be sent out for translation.

    PENDING FURTHER INSTRUCTIONS


## Example Activity

The `activity.py` has been populated with some basic content as a demonstration.

Move these files to a Sugar Desktop Environment, and run these commands:

    ./setup.py genpot
    ./setup.py build
    ./setup.py dev

The icon should appear on the sugar desktop, or in the activities list, and be available for testing.
