
ClickOnce.targets
=======================

ClickOnce.targets is an MSBUILD file that can be used in your continuous deployment pipeline to enable le side by side click once deployment to Development (DEV), Quality Assurance (QA), and Production (PROD) for continuous integration servers.

Problem
---

Click Once depends on each application having a distinct Assembly Name and Product Name and signing everything with some keys. In Visual Studio I find it convenient to distinguish my environments through build configurations. Most of the project settings can vary per build configuration, and it would be nice if the project' publish pane allowed you to set the application details per build configuration, but alas it doesn't. 

The two approaches I see used most are using multiple projects files for the same code base or constantly editing the project publishing configurations. Problems can arise with multiple project files when you add and remove projects from you project they can quickly get out of sync and become a management hassle. And constantly manually updating project files has the risk of human error with every edit and release. 

Solution
--------
This build file is designed to leverage the settings in the existing Visual Studio .csproj files and add additional build properties to specify the ClickOnce publishing options. By default it publishes a DEV variant. This build file doesn't actually deploy the files. 

Objectives
----------

Ideally this could be a drop in addition to the standard click once process that adopts the concept of convention over configuration to specify how to generate publishing targets. It would be nice to simple call msbuild with just the distributed click once target with a PublishTo=DEV, PublishTo=QA, PublishTo=PROD build property with the default being DEV. 

Build Properties
----------------
Required:

 1. $(CSPROJ) - The Project file from which to import default values.
 1. $(CertHash) - Fingerprint of the Certificate in the Cert store which should be used to sign the Assembly.

Conditionally Required:
 If you want to publish anything besides a DEV variant these properties need to be set.

 1. $(ClickOnceIdentitySuffix) - A suffix that will distinguish the click once deployment, Default: DEV
 1. $(ClickOncePublishUrl) - Specify the path where the product of the click once deployment will be saved at the end of the build process.,  Default: $(PublishUrl), TODO: Default to $(PublishUrl)/$(ClickOnceIdentitySuffix)
 1. $(ClickOnceInstallUrl) - Specify the URL from which the Application will be installed., Default: $(InstallUrl)  TODO: Default to $(InstallUrl)/$(ClickOnceIdentitySuffix)

Optional:
 All of these will have relatively sane defaults, but depending on your CI setup you may need to override them depending on versioning practices, where artifacts live, or naming conventions.

 1. $(MAJOR) - Major Version number, defaulted from project.
 1. $(MINOR) - Minor Verion number, defaulted from project.
 1. $(BUILD) -  Revision Version number, defaulted from project.
 1. $(Revision) - Revision Version number, defaulted from project. Recommendation: use .1 = DEV, .2 = QA, .3 = PROD so you can distinguish multiple publishings of the same build.
 1. $(ClickOnceVersion) - Override Assembly Version., Default: $(Major).$(Minor).$(Build).$(Revision)
 1. $(ClickOnceeBinaryPath) - Specify the path to the binaries you want packaged for click once Deployment. , Default: $(OutputPath)
 1. $(ClickOnceAssemblyName) - Specify the Assembly Name, this will be interpreted as Assembly Identity and must be different for side by side deployment. Default: $(AssemblyName) $(ClickOnceIdentitySuffix)
 1. $(ClickOnceProductName) -  Specify the Product Name to be used on the Start menu. Default: $(ProductName) $(ClickOnceIdentitySuffix)
 1. $(ClickOnceApplicationPath) - $(AssemblyName)_$(ClickOnceVersion)\
 1. $(MagePath) - Path to mage binary., Default: C:\Program Files (x86)\Microsoft SDKs\Windows\v7.0A\Bin\NETFX 4.0 Tools\mage.exe


<a href="http://spry-group.com">Work supported by The Spry Group</a>
 


