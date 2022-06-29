---
layout: post
excerpt_separator: "<!--more-->"
title:  "Accessibility on Android: make apps accessible to all the users"
tags: Android accessibility contentDescription ScreenReader TalkBack
---

# Screen Reader: 
- When Screen Reader is turned on, on Android devices, it automatically announces what element is in focus.

- It is recommended use views that Android provides which include view's role and content required by Accessibility service.
If you need a custom button, you extend the Button rather than starting from scratch with a View.
<!--more-->

- In most cases, set element's contentDescription attribute, without including its type/role. 
Screen readers automatically announce both the element's description and type.
For example for a Camera ImageButton, set contentDescription as "Camera". It will announce it as "Camera button, double tap to activate"

- Make sure contentDescription is unique, for example on a recyclerView list of movie, contentDescription of each item could the movie title.

- Exclude TextViews, since Android accessibility services automatically announce the text itself as the description.

- Set android:importantForAccessibility attributes to "no" if there's element added for decoration purpose only, for example background ImageView 
or an element which is not clickable in production but used for testing purpose.

- labelFor can be used along with EditText fields, to announce what the EditText field is meant for.
For example Username: [EditText]
add labelFor attribute to TextView created for Username: label which is a label for Username EditText.

```
<TextView
android:id = "@+id/tvUserName 
...
android:labelFor = "id/etUserName"/>
<EditText
android:id = "@+id/etUserName
...
/>
```
- When required to be added description of an item dynamically we can make use of StringBuilder

```
val contentDescription = StringBuilder()
contentDescription.append("list item").append(movieList[position])
listItemView.setContentDescription(contentDescription);
```

- To override default accessibility screen reader announcements, AccessibilityDelegate can be used on all the views.
Examples:

For a camera ```ToggleButton```, announce as "Turn off camera button, double tap to toggle"

```
cameraToggleButton.setChecked(true);
cameraToggleButton.setTextOn("Turn Off");
cameraToggleButton.setTextOff("Turn On");

ViewCompat.setAccessibilityDelegate(cameraToggleButton, new AccessibilityDelegateCompat() {

     private boolean didPerformAccessibilityAction = false

     @Override
     public void onInitializeAccessibilityNodeInfo(View host, AccessibilityNodeInfoCompat info) {
      super.onInitializeAccessibilityNodeInfo(host, info);
       info.contentDescription = "Camera"
       info.setRoleDescription = "Button"
      
     }
});
```

On a Movie list item, announce as "double tap to view movie detail" and "long tap and hold to add to favorites"

```
ViewCompat.setAccessibilityDelegate(itemView, new AccessibilityDelegateCompat() {

override fun onInitializeAccessibilityNodeInfo(
      host: View, info: AccessibilityNodeInfoCompat) {
    super.onInitializeAccessibilityNodeInfo(host, info)
    val click = 
      AccessibilityNodeInfoCompat.AccessibilityActionCompat(
          AccessibilityNodeInfo.ACTION_CLICK,"view movie detail")
    val longClick = 
      AccessibilityNodeInfoCompat.AccessibilityActionCompat(
          AccessibilityNodeInfo.ACTION_LONG_CLICK, "add to favorites")
    info.addAction(click)
    info.addAction(longClick)
  }

});
```
