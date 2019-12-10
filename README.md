# libSparkColourPicker
This is libSparkColourPicker, a super simple and easy to implement colour picker - designed for iOS 11/12/13.

It simply allows developers to set colours from their preferences, and provides a few useful tools to handle colours in their tweaks.

For users, this library will just be installed as a dependency from another tweak and away you go!

## Get Started
It's super easy to get started with libSparkColourPicker, let's set it up!

### Pre-Requirements
```
None!
```

### Installation for theos
Add the 'libsparkcolourpicker.dylib' file to your "theos/lib" directory, and add the headers to "theos/include"

You will also need to add it to the libraries in your makefile
```
TWEAKNAME_LIBRARIES = sparkcolourpicker
```

I recommend keeping to the same version of libSparkColourPicker for now (as I am actively improving it), so I'd point users to my repo https://sparkdev.me to install it.

You can add it to your control file depends like so:

```
Depends: mobilesubstrate, preferenceloader, com.spark.libsparkcolourpicker
```


### Simple Implementation

I have tried to keep libSparkColourPicker as simple to implement as possible, so tried to make it as similar to the existing "libColorPicker" library as possible.
This means, you can even just switch your library and dependency in your tweak preferences and libSparkColourPicker will just work.

If you want to properly implement SparkColourPicker and have access to potential future changes you need to change your preferences to the following (replacing the "defaults", "key", and "label" values with your own values)

PreferenceBundle plist (Root.plist)
```
	<dict>
		<key>cell</key>
		<string>PSLinkCell</string>
		<key>cellClass</key>
		<string>SparkColourPickerCell</string>
		<key>libsparkcolourpicker</key>
		<dict>
			<key>defaults</key>
			<string>com.your.identifier</string>
			<key>key</key>
			<string>YourCustomColour</string>
			<key>fallback</key>
			<string>#ffffff</string>
			<key>alpha</key>
			<true/>
		</dict>
		<key>label</key>
		<string>Your Colour Label</string>
		<key>key</key>
		<string>YourCustomColour</string>
	</dict>
```
The "Fallback" value is the default value that will be used before the user modifies it, to display in the preview and start with. This can be provided as hex, and in this example is set to #ffffff (White).

Once this is implemented you can then check a colour from within your tweak.
So an example of checking it, depending on the scenario, could be like this:

```
#import "SparkColourPickerUtils.h"

%hook HOOKHERE

-(UIColor*) FUNCTIONTOHOOK
{
    NSString* colourString = NULL;
    NSDictionary* preferencesDictionary = [NSDictionary dictionaryWithContentsOfFile: @"/var/mobile/Library/Preferences/com.your.identifier.plist"];
    if(preferencesDictionary)
    {
        colourString = [preferencesDictionary objectForKey: @"YourCustomColour"];
    }

    UIColor* selectedColour = [SparkColourPickerUtils colourWithString: colourString withFallback: @"#ffffff"];

    return selectedColour;
}

%end

### 'Helper' methods

```
    @interface SparkColourPickerUtils : NSObject
    +(NSString*) hexStringFromColour:(UIColor*) colour;
    +(NSString*) rgbStringFromColour:(UIColor*) colour;
    +(UIColor*) inverseColour:(UIColor*) colour;
    +(UIColor*) colour:(UIColor*) colour withBrightness:(float) newBrightness;
    +(BOOL) colourIsBlack:(UIColor*) colour;
    +(BOOL) colourIsWhite:(UIColor*) colour;
    +(UIColor*) colourWithString: (NSString*) colourString;
    +(UIColor *)colourWithString:(NSString *)stringToConvert withFallback:(NSString*) fallback;
    +(UIColor *)colourWithString:(NSString *)stringToConvert withFallbackColour:(UIColor*) fallback;
    +(UIColor*) colourWithRGBString:(NSString*) stringToConvert;
    +(UIColor *) colourWithHexString:(NSString *)stringToConvert;
    +(BOOL) colourIsLight :(UIColor*) colour;
    +(UIColor*)interpolateFrom:(UIColor*)startColour toColour:(UIColor*)endColour withPercentage:(float)percentage;
    @end
```


### 'Advanced' methods

If you want to add your own implementation instead of using the provided preference cell, you can use the following methods:

```
    @protocol SparkColourPickerViewDelegate <NSObject>
    -(void) colourPicker:(id)picker didUpdateColour:(UIColor*) colour;
    @end

    @interface SparkColourPickerView : UIView

    // forceUIMode is used to force to dark or light mode (-1: Default, 1: Dark, 2: Light)
    -(instancetype) initWithFrame:(CGRect) frame forceUIMode:(int) uiMode;
    -(instancetype) initWithFrame:(CGRect) frame forceUIMode:(int) uiMode andViewSizeFactor:(float)viewSizeFactor;

    // These two properties can be set before showing the view to set the initial colour. The unmodified colour is without alpha.
    @property (nonatomic, retain) UIColor* currentColour;
    @property (nonatomic, retain) UIColor* currentColourUnmodified;

    @property (nonatomic, assign) float currentColourAlpha;
    @property (nonatomic, assign) float currentColourBrightness;

    // The title to display at the top, leave NULL for nothing.
    @property (nonatomic, retain) NSString* keyName;

    // Delegate to call the 'didUpdateColour' method on
    @property (nonatomic, retain) id<SparkColourPickerViewDelegate> delegate;
    
    // This is used for showing alerts.
    @property (nonatomic, retain) UIViewController* rootViewController;
    @end
```

## Authors
SparkDev 2019
