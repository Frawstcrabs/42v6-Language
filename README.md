# 42v6-Language

[42v6][42] is the next version of 42, having all the capabilities of v5 but with
overall better language integration. Notably in v6, we are able to

* Change the outputs of commands.  
* Change the _inputs_ of commands.

If you have any questions, feel free to join 42's [Discord Server][kiwi-hangout]
and asking in the chat. We would be happy to help.

---

### GitHub crash course

If you're new to working with Git, you'll have to do a bit of preparation.

[Relevant article][git-article]

First, if you haven't already, create an account with GitHub, and download a
Git platform ([Git for windows][git], `brew install git`, `apt-get install git`)

Click "Fork" in the top right corner of the Frawstcrabs repo, and then
open up a terminal and go to the folder you want to download into, and then use
`git clone https://github.com/<YourUsername>/42v6-Language` to download the repo

* `git checkout -b branch_name` creates a new working branch
* `git remote add upstream https://github.com/Frawstcrabs/42v6-Language`
    sets the main repo as a source
* Create and/or edit your language files
* `git commit -am 'message'` adds and commits your new files
* `git push -u origin branch_name` copies those files back to GitHub

Then you can make a pull request back on the website. See the guide above for
more detail.

---

### File structure

Each language that 42 can understand must be defined in its own folder in the
root of this repo, and at minimum must have a file called
[`language.xml`](#root-language-file). This means the file structure should look
like this:

* 42v6-Language
  * english
    * language.xml
    * otherstuff.xml
  * your-lang
    * language.xml
    * otherstuff.xml
  * langformat.dtd

`langformat.dtd` is the doctype that all xml files should use. Do not edit this.


### File format

All new language xmls must start with the following lines of code, which both
defines the format of the xml file for use with linters, and also allocates some
entities that will be used throughout the file.

    <?xml version="1.0"?>
    <!DOCTYPE language SYSTEM "langformat.dtd" [
      <!ENTITY call "&#xF000;">
      <!ENTITY defaultcall "&#xF001;">
      <!ENTITY zwsp "&#x200B;">
      <!ENTITY site "https://42.rockett.space/v6/">
    ]>
    
* `&call;` This entity will tell 42 to insert the currently used call character.
    in most cases, this will be the default `+`, but depending on how the server
    is configured, it may be something else. **Do not follow it with a space**,
    as an automatic space will be inserted where required by the selected call.
* `$defaultcall;` This should only be used in `<documentation>` blocks, or places
    where the default call of `+` should be inserted. Do not use a literal `"+"`
    as there may be a situation in future where the default call will need to be
    changed. Using this prevents a lot of searching and fixing. Same spacing rules
    apply here as with `&call;`
* `$zwsp;` This inserts a zero width space character. Usually they will be added
    to english.xml and should be kept where they are in the translations, but
    you can use them in cases where you wish to indent the first line of text,
    or break up any stray @everyone or @here mentions.
* `$site;` This will insert the website URL and can be used for links to
    documentation or other such uses. In general, do not change any URLs that you
    may find in the outputs.

If you want, you can add more <!ENTITY> items to this list and use them however you want,
but these 4 must be present in every file.

### Root language file

A language consists of a `language.xml` file and a number of extension files.  
When the language is loaded, `language.xml`will be read in first in order to
initialise the language, so this is where the language metadata is defined.

The root language file also hosts all the common [outputs](#output-types) that
are used through the entire bots code (such as error messages, formatting,
and permissions).

    <language author=""     <!-- REQUIRED The name(s) of the translators -->
              id=""         <!-- REQUIRED A unique ID for the language (typically 
                                          the name of the language in English) -->
              name="">      <!-- REQUIRED The name of the language (in the language
                                          itself, e.g. 日本語) -->
      <description> <!--REQUIRED-->
        This is the overarching description of 42.
      </description>         
      <output> <!--REQUIRED-->
        This is where the outputs are located
      </output>
    </language>
    

### Extension language files

Commands are hosted inside extension language files. This helps keep things
separated and distinct from one-another and is used to define categories of
commands when they are being viewed on the website. Each extension file will
become its own website page.

[Commands](#commands) are recursive tree like structures, with each subcommand
being a branch of the main command. Here is the structure for an extension file:

    <extension id=""         <!-- REQUIRED This must match the ID in language.xml -->
               site-link=""> <!-- Do not change -->
      <documentation> <!-- REQUIRED -->
        This is the header documentation for the website, and should generally
        explain what the commands in this extension do.      
      </documentation>
      
      <!-- List of command objects for this module -->
      
    </extension>
    

---

### Commands

A command is a set of input and output data for a command that 42 has. If a
command isn't translated when requested by 42, the English output will be used
as a fallback instead.

A command is split up into sections: Inputs, documentation, outputs and
subcommands, where subcommands are just nested `<command>` objects.

Note about arguments in command help: if an argument is wrapped in quotes, it
means the user should provide content in its place. If an argument is wrapped
with square brackets, then providing it is optional. Do not change the order
of arguments, only their names.

    <command id=""          <!-- Do not change. -->
             name=""        <!-- REQUIRED The documented call name for this command -->
             alias=""       <!-- A space separated list of valid aliases -->
             hidden="true"> <!-- Do not change. -->

      <parenthelpsection args="">
        This adds a line of documentation to the parent command's subcommand
        list. Args is the set of arguments that the command takes.
      </parenthelpsection>
      
      <description>
        This is the content that is used in +help. Ensure it is written in the
        first person from 42's perspective.
      </description>
      
      <helpsection args="">
        This adds a line of documentation to this command's usage list.
        Args is the set of arguments that the command takes.
      </helpsection>
      
      <documentation>
        This is the documentation for the command that will be published on
        the website. It should be written in the third person from a developer's
        perspective.      
        If this has type="hidden" or type="parent", then do not change it.
      </documentation>
      
      <output>
        See output types section below.
      </output>
      
      <command>
        These are subcommands which follow the same structure as above.   
      </command>
    </command>
    
    
### Output Types

There are 5 main types of outputs. Lines, Choices, Pluralgroups, Lists and Dicts.
All objects that are in the top level of the `<output>` block will have an id
attribute. This should never be changed as it is required for 42 to reference
the text with.

#### Lines

A line consists of just an ID and a block of text. This can be multiline text.
For every line of text wrapped in a `<line>` block, the leading whitespace will
be stripped away. You can preserve some whitespace by adding a `&zwsp;` before
the space you want to preserve.

    <line id="">Content goes here</line>
    <line id="">
        Or content can go here
        on multiple lines
    </line>
    
Lines are also embedded inside data structures to indicate that a string of text
is the next object in the structure.


#### Choices

Choices are like lines, except every time they are evaluated, only one of the
`<item>` blocks will be returned. `<item>` is just a block of text, but it may
have an optional `weight` attribute. **Either all or none of the items must
define a weight**.

    <choice id="">
      <item>First choice</item>
      <item>Second choice</item>
    </choice>
    <choice id="">
      <item weight="2">This has a 2/3 chance</item>
      <item weight="1">This has a 1/3 chance</item>
    </choice>
    

#### Plural groups

Plural groups allow you to define different outputs based on a numerical input
Usually this is used to have grammatically correct outputs, but is sometimes
used to bundle a pair of outputs under the same ID.
We will go through and add comments to indicate what value is being switched
on and what values are being [inserted](#interpolation) into strings.

A pluralgroup contains a list of `<plural>` objects, in searching order.
A plural should define either `value`, or one or both of `upper` and `lower`
One plural should be defined without any filters to be used as a fallback should
no filters match. In most cases this will be the default "all other values"
option.

    <pluralgroup id="generic-value-print">
      <plural value="1">The user has 1 role.</plural>
      <plural>The user has {} roles.</plural>    
    </pluralgroup>
    
    <pluralgroup id="value-range-demo">
      <plural lower="1000000">User has {} coins in their vault</plural>
      <plural lower="100000">User has {} coins in their safe</plural>
      <plural lower="10000">User has {} coins in their backpack</plural>
      <plural lower="1000">User has {} coins in their purse</plural>
      <plural lower="100">User has {} coins in their pocket</plural>
      <plural>User has {} coins in their hands</plural>
    </pluralgroup>
    
    <pluralgroup id="generic-toggle-print">
      <plural value="1">I have enabled this feature</plural>    
      <plural value="0">I have disabled this feature</plural>    
    </pluralgroup>
    
    
#### Lists

Lists are ordered structures of data that 42 uses as a ordered set of inputs
or as a variable set of inputs.
In the former case, the sentinel `<nonetype/>` may be in the list, in which case
do not remove or modify it.
A list can contain both lines and other objects.

    <list id="variable-demo"> <!-- This is used in +faq in meta_commands.xml -->
      <line>First page of a variable length textbox</line>
      <line>Second page</line>
      <line>Third page</line>
      ...
    </list>
    
    <list id="ordered-data-demo">
      <line>January</line>
      <line>February</line>
      <line>March</line>
      ...
    </list>
    
    
#### Dicts

Dicts are mapping structures of data that 42 uses either as a collection of
related outputs, or as a mapping of user inputs to internally recognised values.

A dict contains multiple `<entry>` elements, which are used to define the name
of the key, and those entry elements can contain a line or any other object.

    <dict id="input-mapping"> <!-- This is used for toggling settings in modlog -->
      <!-- Name is the user input, line is what 42 uses internally -->
      <entry name="enabled"><line>1</line></entry>
      <entry name="disable"><line>0</line></entry>
      <entry name="start"><line>1</line></entry>
      ...
    </dict>
    
    <dict id="output-mapping"> <!-- This is used in +warning -->
      <!-- Name is 42's key, line is what the user sees -->
      <entry name="void"><line>Voided</line></entry>
      <entry name="expired"><line>Expired</line></entry>
      <entry name="expires_never"><line>Does not expire</line></entry>
      ...
    </dict>


---

### Interpolation

Some strings will have the symbol `{}`, or a name/number wrapped in braces.
These are for value interpolation. We will add meaningful names and comments
to resolve confusion, but in most cases it _should_ be easy enough to infer
what each slot will be filled with.

Note about lines with 2 or more `{}` symbols in. Internally these are ordered
and implicitly read as `{0}`, `{1}`, ..., so if you need to swap them around
in your translations, then you should add the numbers in order before moving
the slots around to ensure that the correct values are inserted in the correct
places.

Some interpolation slots related to dates and times may have [% signs][strftime]
inside. You can reorder how dates are formatted by moving these signs around.



<!-- Links -->
[42]: https://42.rockett.space/v6
[kiwi-hangout]: https://discord.me/kiwibots
[strftime]: https://strftime.org
[git]: https://git-scm.com/download/win
[git-article]: https://opensource.com/article/19/7/create-pull-request-github
