# dibbern_info_divisions

# DESCRIPTION

*dibbern_info_divisions* is a generic CFEngine3 based CFDivisions-library containing examples how to use divisions to design a configuration architecture and build configuration stacks for various solutions.

*dibbern_info_divisions* builds upon following CFEngine3 based extensions:

  * CFDivisions : a CFEngine3 module for organizing promises in a modular and goal intention based structure. ( See more about [CFDivisions](https://github.com/sascha-dibbern/CFDivisions) )
  * A collection og generic reusable code to build configuration stacks. (see more about [common_divisions](https://github.com/sascha-dibbern/common_divisions) ) 

# INSTALLATION

The content of *dibbern_info_divisions* can be copied into the CFEngine path `$(sys.workdir)/master/dibbern_info_divisions` on a machine acting as CFEngine hub.

## Enabling divisions

Modify `$(sys.workdir)/master/promises.cf` by adding:

    bundle common divisions
    {
      classes:
        # Non-verbose cfdivisions-run
        !cfdivisions_verbose::
          "dibbern_info_divisions" 
            expression => usemodule (
              "cfdivisions",
               "--library=dibbern_info_divisions --namespace=dibbern_info_divisions --inputs_path=$(sys.workdir)/inputs"
            );
     
        # Verbose cfdivisions-run
        cfdivisions_verbose::
          "dibbern_info_divisions" 
            expression => usemodule (
              "cfdivisions",
                "--library=dibbern_info_divisions --namespace=dibbern_info_divisions --inputs_path=$(sys.workdir)/inputs --verbose"
            );
    }

and modify in "body common control" the "inputs"- and "bundlesequence"-definitions to contain the generated variables from the execution of the "cfdivisions" module. 

    body common control
    {
     
        inputs => {
                   ...
                   "services/main.cf",

                   # Divisions 
                   @(cfdivisions.cfdivisions_dibbern_info_divisions_inputs),

                  ...
        };

        bundlesequence => {
                           ...
     
                           main, # The CFEngine default main 
			  
                           # Divisions 
                           @(cfdivisions.cfdivisions_dibbern_info_divisions_bundlesequence),

                          ...
        };
     }

## Enable documentation for divisions

CFDivisions can generate perldoc PODs and man3 documentation from divisions this divisions-library.

    bundle common divisions
    {
      classes:
        ...

        # Cfdivisions documentation-run
        cfdivisions_documentation::
          "dibbern_info_divisions_documentation" 
            expression => usemodule (
              "cfdivisionsdoc",
              "--library=dibbern_info_divisions --namespace=dibbern_info_divisions --inputs_path=$(sys.workdir)/inputs"
            );
        ...
    }

# PRINCIPLES

*dibbern_info_divisions* is build around following principles:

  1. Configuration logic only : artefacts like data and configuration files or templates are placed explicit into artefact-code-repositories, that are retrieved through artefact management from CFDivisions-library *common_divisions*.
  2. No secrets : any secrets like passwords, keys and so on are also excluded from the code of this library and will only handled indirect through artefact management.

# HISTORY

Around 2010 the author (Sascha Dibbern) developed a first version of a CFEngine3-module called *cfdivisions* that enabled developing CFEngine3 code in a goal oriented way. One essential part of goal oriented development is that higher goals build upon realizations of lower goals. This concept was nearly archived in CFEngine3's language like working with the promise-metaphors. But unfortunately the procedural way of using a central bundlesequence delegating to bundles, that delegate other bundles and so on, broke the idea of easily making a goal oriented design of the code. Why ?

  * Bundles tended to becoming big and include too much. Their logical runtime state is then hard to grasp.
  * Dividing bundles to smaller bundles created many bundles which needed to be managed and sequenced. A demand of bundle dependencies the arises.
  * Bundles with promises would depend on other bundles and promises especially, when the configuration-scripts were designed to respect architectual layers. These dependencies are hard to guess and manage when having a language that in its core still behaves procedural.

*cfdivisions* introduces dependency based goal-management known from build tools like *make* or Java's *maven*.
After developing *cfdivisions* the the authors own CFEngine-scripts were refactored to be division-based ("the old library"). At this time functionality of the division-libraries *dibbern_info_divisions* and *common_division* were still mixed. 
In april 2016 a migration of the authors old cfdivsions-based library from CFEngine 3.2 to 3.7 started. During this migration the author decided to refactor and publish the *cfdivisions* (the CFDivisions-module). New ideas and concepts were introduced like:

  * active usage of CFEngine namespaces to align division library naming to code
  * autocreation of documentation from divisions
  * test features like enabling, disabling and overriding/mocking divisions giving the opportunity to unit-test CFEngine code
  * better debugging of *cfdivisions* output

Also the inner qualities of *cfdivisions* changed from being an ad hoc script to an tool with higher quality code:

  * refactoring to object oriented code with clear code design instead of a spaghetti script. This enables future improvements easier to be implemented.
  * detailed unit- and integration-testing of underlying the Perl code
  * making the code of *cfdivisions* distributeable as tar.gz, rpm or what else the Perl-buildtool *dzil* (DistZilla) can build as deployable packages.
  * documenting *cfdivisions* from concept to examples

Also a refactoring of the written old divisions-library was made to the current two libraries:
  * *common_divisions* : a generic library of reusable promises and bundles structured as divisions
  * *dibbern_info_divisions* : a system specific library designed around the architecture and design of the authors own IT.

*dibbern_info_divisions* now provides "logic only" divisions with no artefacts (especially security relevant).  
The library is and will be published as an reference and example for showing the usage of CFDivisions.

# SEE ALSO

  * [CFDivisions](https://github.com/sascha-dibbern/CFDivisions) : The underlying CFEngine3 module to enable divisionbased components
  * [common_divisions](https://github.com/sascha-dibbern/common_divisions) : A collection og generic reusable code to build configuration stacks

# LICENSE

See LICENSE file.

# AUTHORS

 *  Sascha Dibbern [http://sascha.dibbern.info/](http://sascha.dibbern.info/)
    (email: sascha at dibbern.info)

