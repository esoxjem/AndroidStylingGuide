# Android Styling Guide
A simple guide for Styles and Themes


## Style
If you have a `View` with some attributes on it, you can extract them into a *style*.
 
```xml
<!--- No Style --->
<View android:background="..."/>

<!--- Styled --->
<View style="@style/MyStyle"/>

<!--- styles.xml --->
<style name="MyStyle">
	<item name="android:background">#ffffff</item>
</style>

```

This is useful because you can apply the *style* to multiple views.


### When To Style
Use styles when you have multiple **semantically identical view**, i.e, all of the views mean exactly the same thing.

- Buttons on a calculator.
- Primary and secondary buttons in an app.

### When NOT To Style
- Single use styles.
	- There is no particular advantage to styling these views.
	- No reuse and mostly noise.
			
- Views that use same attributes but are semantically different.


```xml
<TextView 
	android:id="@+id/title"
	android:textColor="@color/blue"/>
	
<TextView 
	android:id="@+id/body"
	android:textColor="@color/blue"/>
```

Extracting a common style to reduce code duplication for title and body would just couples them and doesn't allow us from styling them differently in the future.

Analogous to using the same constant for two different concepts.

```kotlin
const val NUM_THREE = 3

const val COLUMNS = NUM_THREE
const val RETRIES = NUM_THREE
```
### Extracting Styles
Use built in refactoring menu. 

```
Refactor > Extract > Style
```

### Inheritance
Styles can have parents and children.

```xml
<!--- parent --->
<style name="Action"/>

<!--- explicit parenting --->
<style name="Primary" parent="Action"/>
<style name="Secondary" parent="Action"/>

<!--- implicit parenting --->
<style name="Action.Primary"/>
<style name="Action.Secondary"/>
```

- Don't mix both parenting styles. It's misleading.
- Prefer Implicit over Explicit parenting. Easier to read.

When dealing with the platform, use dotless child for simplicity.

```xml
<style name="BorderlessButton"
		parent="Widget.AppCompat.Button.Borderless" />
		
<style name="BorderlessButton.Primary"/>
<style name="BorderlessButton.Secondary"/>
```

### Multiple Styles
- Not possible on Android, unlike CSS. 
- Other than `TextView` using `TextAppearance`.

Overriding priority:

```
view styling > style > textApprearance
```

### Organization
For starters,

- Attributes `android:layout_****` should be stay in the layout XML
- Attributes `android:****` should be defined in a style XML.

```xml
<TextView
    android:id="@+id/name"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_alignParentRight="true"
    android:text="@string/name"
    style="@style/FancyText"/>
```

The idea is to keep only layout (positioning, margin, sizing) and content attributes in the layout files, while keeping all appearance details (colors, padding, font) in styles files. 

This rule has exceptions, but in general works fine.

- `android:id` should obviously be in the layout files.
- `android:orientation` for a `LinearLayout` makes more sense in layout files.
- `android:text` should be in layout files because it defines content.


## Themes
*Styles* are local to a view, *Themes* are applied system-wide.

- Setup defaults. ex: spinnerItemStyle.
- Activity level theming.
- Enables theme swapping. Light vs Dark.
- Different scope than styles

```xml
<!--- view scope --->
<style name="MyStyle">
	<item name="android:background">@color/blue</item>
</style>

<!--- app/activity scope --->
<style name="MyTheme">
	<item name="android:windowBackground">@color/white</item>
</style>

```

- Post Lollipop: Use view level theming if you want a portion of your app to look different than the rest of your app. Apply to a `ViewGroup`.
- Try modifying the theme first, then move to local styling.

## Miscellaneous
### Naming
Prefix the type info: `type_foo_bar.xml`.

- `fragment_contact_details.xml`
- `view_primary_button.xml`
- `activity_main.xml`

### colors.xml is a color palette
- `colors.xml` should be a mapping from a color name to an RGBA value. 
- Abstract underlying colors from their usage in the app.

```xml
<!--- Bad --->
<resources>
    <color name="button_foreground">#FFFFFF</color>
    <color name="button_background">#2A91BD</color>
</resources>    

<!--- Good --->
<resources>
    <color name="white">#FFFFFF</color>
    <color name="blue">#2A91BD</color>
</resources>
```

- Further separation between underlying colors and style usage can be achieved by defining an additional color resource file which references the color palette.

```xml
<!--- color_mapping.xml --->
<color name="button_foreground">@color/white</color> 
<color name="button_background">@color/blue</color> 

<!--- styles.xml --->
<style name="AcceptButton">
    <item name="android:foreground">@color/button_foreground</item>
    <item name="android:background">@color/button_background</item>
</style>
```

Cost of maintaining another set of color mappings increases.

### Treat dimens.xml like a palette
- Define a "palette" of typical spacing and font sizes.

```xml
<resources>
    <!-- font sizes -->
    <dimen name="font_larger">22sp</dimen>
    <dimen name="font_large">18sp</dimen>
    <dimen name="font_normal">15sp</dimen>
    <dimen name="font_small">12sp</dimen>

    <!-- typical spacing between two views -->
    <dimen name="spacing_huge">40dp</dimen>
    <dimen name="spacing_large">24dp</dimen>
    <dimen name="spacing_normal">14dp</dimen>
    <dimen name="spacing_small">10dp</dimen>
    <dimen name="spacing_tiny">4dp</dimen>

    <!-- typical sizes of views -->
    <dimen name="button_height_tall">60dp</dimen>
    <dimen name="button_height_normal">40dp</dimen>
    <dimen name="button_height_short">32dp</dimen>

</resources>
```

- Use the `spacing_x` dimensions in margins and paddings.

### strings.xml

- Name your strings with keys that resemble namespaces. 
- Namespaces are necessary to bring context and break ambiguity.

```xml
<!--- Bad --->
<string name="network_error">Network error</string>
<string name="call_failed">Call failed</string>

<string name="enter_email_here">Enter email here</string>
<string name="enter_name_here">Enter name here</string>

<string name="logout">Logout</string>
<string name="contact_us">Contact Us</string>


<!--- Good --->
<string name="error_network">Network error</string>
<string name="error_call">Call failed</string>

<string name="registration_email_hint">Enter email here</string>
<string name="registration_name_hint">Enter name here</string>

<string name="settings_log_out">Logout</string>
<string name="settings_contact_us">Contact Us</string>

```

- Don't write string values in all uppercase. 
- Use attributes for in the view instead. ex: `textAllCaps` for all caps.

```xml
<!--- Bad --->
<string name="error_call">CALL FAILED</string>

<!--- Good --->
<string name="error_call">Call failed</string>
```
