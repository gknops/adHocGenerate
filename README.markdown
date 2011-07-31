NOTE: see also the companion article at [http://gknops.github.com/adHocGenerate](http://gknops.github.com/adHocGenerate).

# adHocGenerate

`adHocGenerate` is a script designed to be used in a *Post Action* of an Xcode4's *Archive* action.

It produces all data required for wireless ad hoc distribution of an iOS application:

- IPA archive with embedded *mobileprovision* (which must contain the intended users UDIDs)
- Manifest
- HTML file containing the necessary link to trigger wireless installation.

## Installation

To install `adHocGenerate` first clone the git repository from github. In a terminal window change to the directory you want to clone the repository to, then clone it:

	cd /some/directory
	git clone git://github.com/gknops/adHocGenerate.git

There are several ways how you can use the script:

### Using from cloned repository

You can use the script directly from the repository. For this use the complete path inside the *Post Action*, for example:

	/some/directory/WirelessdHoc/adHocGenerate

### Copying or linking to a directory inside PATH

You can copy or symlink `adHocGenerate` to a directory amongst your `PATH`s. This makes it possible not to use a path at all when invoking the script inside the *Post Action*.

### Copying to your project

If you share this project with others, it might be best to copy the script into your project and add it to your source code repository in order to avoid external references. For example if you copy it into a `AdHoc` directory inside your project, you can use

	${PROJECT_DIR}/AdHoc/adHocGenerate

as path inside your *Post Action*.

## Configuration

The script is controlled via environment variables and an optional template for the `HTML` page.

It is run from a *Post Action* of the *Archive* action, so that it is run every time you archive your product. Optionally duplicate your Scheme first if you do want control over when the AdHoc distribution data is generated.

Bring up the *Scheme Editor*, click the disclosure triangle before the *Archive* action, and click the `+` button. Select *New Run Script Action* from the drop down menu, and fill it in like in this example:

	AdHoc_TITLE="Tile_For_Your_App_Here"
	AdHoc_IDENTITY="The_Name_Of_Your_Distribution_Identity"
	AdHoc_PROVISONING_PROFILE="/See/Below/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX.mobileprovision"
	AdHoc_DESTDIR="/Where/To/Store/Files"
	AdHoc_IPA_URL="Absolute_URL_Of_IPA_On_Your_Web_Server"
	
	mkdir -p $AdHoc_DESTDIR
	
	export AdHoc_TITLE AdHoc_IDENTITY AdHoc_PROVISONING_PROFILE AdHoc_DESTDIR AdHoc_IPA_URL
	
	# May need to include the path
	/some/path/adHocGenerate

Change `/some/path/adHocGenerate` in the last line to a path as discussed in the [Installation][] section.

The various environment variables are described below.

### Mandatory environment variables

These required environment variables are:

#### AdHoc\_TITLE

A title for your app (human readable).

#### AdHoc\_IDENTITY

This is the *Identity Name* of your developer profile used for distribution. You can find it in the Origanizer's *Devices* tab, on the left select *Developer Profile* in the *LIBRARY* group.

#### AdHoc\_PROVISONING\_PROFILE

The path of your AdHoc distribution profile. To find that select the Origanizer's *Devices* tab, on the left select *Provisioning Profiles* in the *LIBRARY* group. Right- or Control-click on the correct Profile, and select *Reveal Profile in Finder* from the context menu. Tip: drag the file onto a Terminal window, the full path will appear and you can easily copy it.

#### AdHoc\_DESTDIR

A directory on your disk where to place the generated files.

#### AdHoc\_IPA\_URL

The absolute URL the IPA file will have on your web server.

### Optional environment variables

#### AdHoc\_NAME

Used as file name for the `IPA` and manifest files, defaults to the content of the `TARGETNAME` environment variable.

#### AdHoc\_HTML\_INDEX\_NAME

The name used for the *HTML* file, defaults to `index.html`.

#### AdHoc\_PLIST\_URL

The full URL for the manifest. Defaults to the content of the `AdHoc_IPA_URL` environment variable, replacing the `.ipa` extension with `.plist`.

#### AdHoc\_HTML\_TEMPLATE\_PATH

By default `adHocGenerate` will use a very simple built-in template for the *HTML* file. You can however provide your own template. As an example you could add a file named `AdHocTemplate.html` file and place it into a `AdHoc`subdirectory in your project. In that example you would set `AdHoc_HTML_TEMPLATE_PATH` to `${PROJECT_DIR}/AdHoc/AdHocTemplate.html`. See the next section for a description of this template.

## Optional HTML template

The HTML template (see the description of the optional `AdHoc_HTML_TEMPLATE_PATH` environment variable in the previous section) can have arbitrary content. `adHocGenerate` will replace variables inside that template, these variables are surrounded by dollar signs, for example `$variable$`.

Available are all environment variables Xcode provides, plus all of the `AdHoc_*` variables you added to the *Post Action*.

In addition if not defined by you, `adHocGenerate` will provide a variable named `AdHoc_ShortVersion` which will contain the `CFBundleShortVersionString` value of your apps `Info.plist` file.

## Copying to web server

Because it is easy to add another *Post Action* to copy the generated files to a web server, `adHocGenerate` does not take care of that. See the CocoaHeads BlitzTalk document at [http://gknops.github.com/adHocGenerate](http://gknops.github.com/adHocGenerate).

## Debugging

Note that unfortunately Xcode's log does not contain any error messages a *Post Action* generates. Please check the syslog instead, for example with *Console.app*.


