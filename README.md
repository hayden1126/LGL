# Slightly modified LGL 

[![Build Status](https://teamcity.eskaduren.se/app/rest/builds/buildType:(id:Phdeis_BasicRunLgl)/statusIcon.svg)](https://teamcity.eskaduren.se) ![Github build all](https://github.com/flindeberg/LGL/workflows/Build%20and%20install%20CI/badge.svg) ![Github test all](https://github.com/flindeberg/LGL/workflows/Testrun%20CI/badge.svg)


All files distributed with LGL fall under the terms of 
the GNU General Public License, and are copyright (c) 2002, 2003 Alex Adai.

Changes dnd updates copyright (c) 2004-2020 Barrett Lyon

Addtional changes done in this fork copyright (c) 2019, 2020 Fredrik Lindeberg

LGL on the web at:  http://www.opte.org/lgl/ 

Much thanks to the Marcotte lab for testing. 

If you use this in your research, please cite (if possible):
- Lindeberg, Fredrik. “Coordinating the Internet.” Licentiate Thesis, Linköping University, 2019. http://urn.kb.se/resolve?urn=urn:nbn:se:liu:diva-161812.
- Lyon, Barrett. "The Opte Project." Mapping the Internet, Self, 2004. http://www.opte.org.
- Adai, Alex T., Shailesh V. Date, Shannon Wieland, and Edward M. Marcotte. “LGL: Creating a Map of Protein Function with an Algorithm for Visualizing Very Large Biological Networks.” Journal of Molecular Biology 340, no. 1 (2004): 179–90. https://doi.org/10.1016/j.jmb.2004.04.047.


# Example output

![The Internet 2003](http://www.opte.org/wp-content/uploads/2014/04/about-img-2.png)

Example of the Internet using traceroute vs BGP in 2003. 


![Image of the Internet](https://raw.githubusercontent.com/flindeberg/LGL/master/resources/images/internet_2016.png)

An example of the Internet as generated by data from 2016. The grapher can in theory handle any kind of data in ncol-format.

# Requirements

A heap of stuff, in no particular order:

- A C++ compiler
- - Boost library required (I should have fixed version issues, but I have not future proofed it)
- bgpdump (https://bitbucket.org/ripencc/bgpdump/wiki/Home)
- - Seems broken in Debian distros, so compile from source if needed
- perl (5+, I think)
- Java (8 seems to work)
- Xserver installed for graphical tools (works well under WSL 2 in Windows)
- Python 3 (there are bash scripts lying around as well, but the python scripts are 3x faster)

# Installation

Use the Makefil, i.e.

    prompt$ make 
    prompt$ make install # local install in $(PROJECTDIR)/bin

should do the trick in the root directory (feel free to improve the magic
`setup.pl -i` script which does a lot of suspect lifting).

# Making Internet graphs

If your intention is to graph custom stuff, just hack away. Below is how you quite easily
can make graphs from bgp-dumps. You need a separate folder for each project, due to design
in the original LGL library. Below follows an example for a graph for a 2000 Internet. Takes 
around 10 minutes on a fairly modern computer (8 threads or so) with a decent Internet connection. 

The oneliner which creates a graph, including bootstrapping, from 2000-09-01:
    
    prompt$ cd scripts/
    prompt$ ./creategraphfromdate.sh 2000 09
    prompt$ # doing magic, and creating a graph
    prompt$ # arguments to creategraphfromdate are year and month
    prompt$ # and the scipt lacks proper error handling

The same thing but step by step (if above fails):

    prompt$ cd scripts/
    prompt$ ./create_run.sh internet_2000
    prompt$ cd ../testrun/internet_2000
    prompt$ wget http://data.ris.ripe.net/rrc00/2000.09/bview.20000901.0610.gz
    prompt$ ./bootstrap.sh bview.20000901.0610.gz
    prompt$ # doing magic, and creating a graph
    prompt$ # by default generating a 2400x2400 png (change run.sh for different resolution)
    prompt$ # should be a 'internet_2001.png' in 'testrun/internet_2001' if all went well
    
 Also possible:
 
    prompt$ cd scripts/
    prompt$ ./creategraphfromurl.sh http://data.ris.ripe.net/rrc00/2000.09/bview.20000901.0610.gz
    prompt$ # wait for magic, by default generating a 2400x2400 png (change run.sh for different resolution)
    prompt$ # should be a 'view.20000901.0610.png' in 'testrun/bview.20000901.0610' if all went well
 
Replace the bview-file with a more recent one for a larger and newer network network. Coloring is
set in `perls/colorEdgesBasedOnLevel.pl`, currently a mix of greenish and bluish tints going on white
at the edges. 

A short disclaimer; the graph is only as good as your data. The `bootstrap` script works for generating interesting graphs. Are they 100% correct? I don't know, you are welcome to check and improve the code! There might be BGP-quirks I do not know of, even though I catch the vast majority of bgp announcements. 

# Additional reading

User guide to LGL, helped me figure out some important things:
http://clairemcwhite.github.io/lgl-guide/

Previous README data:
https://github.com/TheOpteProject/LGL/

Getting up to speed on Internet routing:
http://networkingbodges.blogspot.com/2019/04/a-real-full-internet-table-in-lab.html
https://www.noction.com/blog/as-path-and-as-path-prepending

# Old readme
## Table of contents

  0 Before compiling!
  I Setup and Installation
 II Other files that come with LGL
III Expanding LGL
IV  What's new for 2.0

## Before compiling!

Firstly, LGL will probably only compile with the GNU compilers. It was tested 
on FreeBSD 9.x and CentOS, but it should compile OK on other Linux 
distributions. For other operating systems you are on your own. Good Luck :-)

You must have the following Perl modules in your @INC path to run LGL:

ParseConfigFile.pm
LGLFormatHandler.pm

These files are in the ./perls directory.  You don't have to know anything 
about these modules, and you won't have to use them directly but lgl.pl will 
call them.

## Setup and installation

To compile LGL change to the same directory as setup.pl and type:

    prompt$ ./setup.pl -i

This will compile 2D and 3D versions of LGL and put the resulting binaries 
in the ./bin directory. Afterwards you can move them whereever you want.


NOTE:  setup.pl has been updated to locate boost, however you may need
       to direct gcc on where to find the includes if the automated 
       detection does not work:

       env CPLUS_INCLUDE_PATH=/usr/local/include ./setup.pl -i

After all is compiled and done you can run LGL by the driver script lgl.pl as:

    prompt$ ./bin/lgl.pl edges_file

but you have to modify the 'tmpdir' variable in lgl.pl. That directory will 
hold all the files that LGL outputs, and it must be changed for EACH run.  
However, The best way to run LGL is to have setup.pl generate a sample 
config file for lgl.pl by running it as

    prompt$ ./setup.pl -c conf_file_name

That file (after modification of course) can just be given to lgl.pl for 
execution as follows:

    prompt$ ./bin/lgl.pl -c conf_file_name

The config file itself is documented further, and explains each of the 
variables to be used.  It also provides defaults, so the minimum that MUST 
be changed are the variables:

    tmpdir
    inputfile

where tmpdir is the output directory of the LGL run and inputfile is the edge 
file. inputfile must be a file parsable by LGLFormatHandler.pm This can be 
just a simple 2 column space delimited file with one edge per line 
(the 2 vertex ids represent the two columns).

One last change is to ./bin/lgl.pl. A Perl variable $LGLDIR must be set to 
the root bin directory of all the lgl executables (This might be 
/where/lgl/was/unpacked/bin ). This var is empty by default, and the 
program won't run until it is set correctly.


## Other files that come with LGL

A list of important files in the perls dir:

    genVrml.pl - This generates the VRML code from 3D layout results. Run 
    genVrml.pl with no args to get the usage.

colorEdgesBasedOnLevel.pl - This generates a simple color file to be 
given to lglview, that will color your edges based on the level in the 
heirarchy in layout.

Other files might be included as well, but they are not necessary for LGL. 
Their documentation will be added here in the near future, or they may not 
be carried in the future.

### Other Files:


LGLView.jar - A JAVA 2D viewer for looking at the output of lgl.pl. The output of the layout programs is just a set of coordinates.  For looking at 2D 
coordinates use lglview.jar See the web page http://www.opte.org/lgl for 
usage and other info.

ImageMaker.jar - A JAVA 2D image output tool.  For more detail visit http://www.opte.org/lgl/

Looking at the huge PNG (100k x 100k pixels) java.awt.image.Raster: The 
maximum width x height has to be less than Integer.MAX_VALUE (2147483647) so 
the maximum square image is 46340 x 46340. Note also that such images will 
need a lot of RAM since Java's BufferedImage's pixels are hold in memory. 

An example of a larger output would be: 

    java  -Xms1G -Xmx5G -jar ImageMaker.jar 29200 29200 <files...>

    LGLView.jar - The full package that combines LGLView and ImageMaker.

    Java - Directory and source code of all JAVA programs.  See README in the JAVA dir.


## Expanding LGL

The most obvious way to expand LGL is to add support for your type of edge file to LGLFormatHandler.pm.  Just add a method to read in your file type, update 
the 'loadFromFile' method to recognize your file suffix, and that should be it.

Let me know of any source code contributions that would make LGL more suitable 
and usable so I can add the code in!


## What's new for 2.0

LGL-2.0 is the first new release of LGL in years.  It's the first release by 
the new LGL team at the Opte Project.  LGL was the baby of Alex Adia and 
he's been too busy to update things, now we're involved in bringing the 
code up-to-date. 

2.0 changes include:  

- The code has been ported to boost-1.55
- Now compiles on modern operating systems
- LGLView.jar has been updated to work with Java 1.6
- ImageMaker.jar has been released with the ability to do large resolution (46340 x 46340 pixel output).
- UI fixes for lglview.jar
- The original code uses Jama, the old classes were included with the software package, we've now changed that to use the JAR from Jama RI.

Cheers 
blyon@opte.org


