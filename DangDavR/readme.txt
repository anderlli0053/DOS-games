#=============================================================#
#  uGrab - An EGAGRAPH Editor For Commander Keen Source Mods  #
#              Copyright (c) 2019-2020 K1n9_Duk3              #
#-------------------------------------------------------------#
#       "I grab. You grab. We all grab -- with uGrab!"        #
#=============================================================#


So What Is This Thing?
======================

Well, it's a GUI-based editor that allows you to edit certain parts of an
EGAGRAPH file without having to manually rename tons of files or having to
edit tons of values in a text file.

After adding a couple of new sprites for the "Foray in the Forest" source mod
and having to rewrite half of the ModId definition file for that game every
single time, I decided to write this tool as a ModId replacement.


Features
========

 - read and write EGAGRAPH files
 - import graphics in any format, including  BMP, PNG, GIF and JPEG (...WTF?)
 - export graphics as fully ModId-compatible bitmaps
 - add new sprites and move them to any position without having to rename files
 - edit sprite hitboxes by pointing and clicking instead of just typing numbers
 - add new elements by dragging and dropping files onto the uGrab window while
   the program is running
 - automatically remove any unnecessary transparent parts from sprites and
   adjust the offset values


Adding New Sprites
==================

I recommend using the ModId definition file to load your current project, since
that will allow uGrab to assign the correct types and names for the EXTERN
chunks. As of version 1.0101, you can also use the ModId definition file for
importing from the EGAGRAPH file, not just for importing from a bitmap folder.

The main reason why importing via a ModId definition is recommended is that
uGrab and ModId can export the Terminator chunks as bitmaps and the text chunks
as text files if you import via the ModId definition file. If you import from
the EGAGRAPH file without a ModId definition file, the EXTERN chunks will be
exported as raw binary files by default unless you manually assign the correct
chunk type to each chunk. Since the chunk types are not saved in the EGAGRAPH
file, you would have to do this every single time after importing from the
EGAGRAPH file.

To add a new sprite, you need to open the "Sprites" tab first. Then you can
either click the "Add" button to add a new sprite image, or you can drag image
files onto the uGrab window to add them to the current tab.

uGrab will automatically convert any high- or true-color image into an indexed
image if it needs to. The colors are mapped to the best match in uGrab's color
palette.

Once uGrab has converted the image, it will check if the sprite image is in
ModKeen format (image, mask & hitbox in one bitmap) or ModId format (image &
hitbox in one bitmap) and convert it if necessary. The hitbox part will be
scanned and used to set the initial hitbox data for the sprite. If the image
does not have a hitbox section, uGrab will create a hitbox that covers all non-
transparent parts of the image.

Of course, it's always a good idea to adjust the sprite's hitbox and origin
values after adding the new sprite.


Note that the hitbox is not just used for hit detection in-game. It is also used
to determine if a level object is visible!

The numbers at the bottom of the image preview in the sprite tab show the origin
and hitbox values that will actually be stored in the EGAGRAPH file. These are
in "global" units (16 global units = 1 pixel), and the hitbox coordinates are
stored differently than in ModId's sprite definition files. ModId stores the
hitbox coordinates relative to the top-left corner of the sprite image. The
values in the EGAGRAPH file are stored relative to the origin of the sprite.
Just ignore these values and use the hitbox editor if you're confused.


Using The Hitbox Editor
=======================

In the hitbox editor, you use the mouse to change the hitbox and origin of a
sprite. Press and hold the left mouse button to draw the hitbox on top of the
sprite image. Right-click to set the origin.

Use the arrow keys to scroll the view when you need to. The [+] and [-] keys on
the num block can be used to change the zoom level.

As of version 1.008, you can also use the keyboard to change the origin and the
hitbox size in the hitbox editor. Simply use the keys [1] to [4] to select the
different modes:

 [1] - arrow keys scroll the view (default mode, as in previous versions)
 [2] - arrow keys move the sprite's origin
 [3] - arrow keys move the top-left corner of the sprite's hitbox
 [4] - arrow keys move the bottom-right corner of the sprite's hitbox


Optimizing Your Sprites
=======================

uGrab can scan all your sprites and automatically remove all fully transparent
parts of the sprite image to reduce the amount of memory required by the
sprites.

But before you can use this feature, you need to find the sprite that is used
for the scorebox in the game and set the "protect" checkbox for that sprite.
Optimizing this sprite would cause issues in the game, because the game changes
the sprite image to make it display the score, ammo and lives numbers. The code
responsible for updating this sprite will not work correctly anymore if the
size of the sprite changes, which is why uGrab shouldn't be allowed to
"optimize" this particular sprite.

uGrab will show a warning message if there are no protected sprites to remind
you that you need to set the "protect" option for the scorebox sprite before
starting the optimization process.


There are two different optimization settings. The default ("safe") setting is
designed for use with the original ID Engine, where the game creates pre-shifted
versions of each sprite based on the sprite's "shifts" setting.

The second setting ("full" optimization) is for source mods that use lemm's
new sprite shifting code, like FORAY IN THE FOREST and SIGN OF THE VOOL. If this
setting is selected, uGrab will ignore the "shifts" setting and assume the
drawing code will be able to shift the sprite to any pixel position. Don't use
this option if you're not sure if your mod is using code that allows this to
work correctly in-game.


You can also use this feature to optimize your workflow when adding new sprites:
Since uGrab can remove the unused parts of the image, you can let your new
sprite bitmaps all have the same size and adjust the relative position of the
image in the bitmap itself. Then you can import them without having to assign
the correct offset values for each sprite. Just import the bitmaps and select
the "Optimize Sprites" option.


Using uGrab As A ModId Replacement
==================================

A bug in the hitbox drawing code means that ModId cannot export certain sprites
correctly, especially sprites that have been optimized by uGrab.

As of BETA Version 1.005, uGrab accepts the most important ModId command line
parameters, so you can use uGrab instead of ModId until somebody fixes the bug
in ModId.

Supported command line parameters are:
   
 -export	-> convert EGAGRAPH to BMP
 -import	-> convert BMP to EGAGRAPH
 -gamedef=...	-> selects name of ModId definition file
 -bmpdir=...	-> selects BMP directory
 -gamedir=...	-> selects game directory (where the EGAGRAPH files are)
 -palette=...	-> selects a bitmap whose palette should be used for exporting

Keep in mind that uGrab is not fully compatible with ModId. ModId is able to
extract files that are embedded in the game's executables and ModId supports
the graphics formats of Keen: Vorticons and other games. uGrab does not provide
any of this.


Known Issues
============

I encountered multiple issues while developing this tool. Most of these are
related to BlitzMax, the programming language that I used to create this tool.
As it turns out, the MaxGUI modules are a steaming pile of crap. That means
most of the following issues are not going to get fixed in future releases.

 - mouse wheel does not work for scrolling the image previews
 - image previews stop working at a certain zoom level
 - image previews don't update while dragging the scroll bars
 - cannot select multiple entries from the lists (for deleting or moving)
 - cannot hold down multiple arrow keys to scroll in the hitbox editor
 - cannot drag and drop more than 256 files at once

 - no support for CGA or VGA graphics
 - cannot change palette
 - "protect" settings must be set manually after importing the data


Version History
===============

BETA Version 1.011	(2020-01-31)
------------------------------------

 - added support for EXEFILE directive in ModId definition files to extract
   EGAHEAD and EGADICT from the game's executable.

 - added support for EGAGRAPH directive in ModId definition files to support
   Keen Dreams (not fully, though!)

 - added support for GRAPHICSFORMAT directive in ModId definition files to
   support CGA and VGA graphics in future versions ... MAYBE!

 - added built-in UNLZEXE to decompress the game's executable if necessary

 - added an option to keep the current EGADICT when exporting
 

BETA Version 1.010	(2020-01-25)
------------------------------------

 - added error handling back in (was accidently left disabled in 1.0009)

 - added support for bugged EGAHEAD files missing the final offset
   (adds an extra chunk if last offset doesn't match EGAGRAPH size)

 - added an option to open EGAGRAPH files via ModId definition files
   (assigns the correct chunk types and names to the EXTERN chunks)


BETA Version 1.009	(2019-11-13)
------------------------------------

 - improved EGAHEAD type (4-byte offsets or 3-byte offsets) detection


BETA Version 1.008      (2019-09-13)
------------------------------------

 - fixed a bug in the EGAGRAPH chunk type detection code
   (correct sprite settings will now be recognized as such)

 - added dedicated "Export" and "Replace" buttons to export or replace sprites
   and other chunks without having to use a full ModId export every time or
   having to move chunks around a lot when trying to replace a chunk

 - masked pics can now be imported from and (exported as) ModKeen bitmaps
   (bitmaps with separate mask pixels)

 - masked bitmaps (bitmaps with "transparent" pixels) can no longer be used for
   the unmasked tile sets (TILE8, TILE16)

 - keys can now be held down in the hitbox editor, but not multiple keys at the
   same time

 - added keyboard controls for hitbox editor


BETA Version 1.007      (2019-08-07)
------------------------------------

 - fixed a bug in the hitbox editor
   (changing the origin didn't work correctly after scrolling around)


BETA Version 1.006      (2019-07-14)
------------------------------------

 - improved error detection when importing from ModId Definition or using the
   "-import" parameter


BETA Version 1.005      (2019-07-13)
------------------------------------

 - added command line parameters for using uGrab as a ModId replacement


BETA Version 1.004      (2019-07-09)
------------------------------------

 - added the sprite optimization feature


BETA Version 1.003      (2019-07-05)
------------------------------------

 - when adding sprite bitmaps in ModId or ModKeen format, uGrab will now use
   the non-transparent parts of the bitmap to generate the hitbox data if the
   hitbox-part of the bitmap is completely empty


BETA Version 1.002      (2019-05-11)
------------------------------------

 - center of the hitbox ("midx") is listed in the Sprites tab


BETA Version 1.001      (2019-04-27)
------------------------------------

 - added palette checking when adding a bitmap image


BETA Version 1.000      (2019-04-26)
------------------------------------

 - first release