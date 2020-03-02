---
title: material design motion pattern
date: 2020-03-02 11:36:18
tags:
  - English
  - MaterialDesign
---

The motion system is a set of transition patterns that can help users understand and navigate an app.

Material Design's motion system is comprised of four patterns for transitioning between components or full-screen views. The patterns are decided to help users navigate and understand an app by reinforcing relationships between UI elements.

The transition patterns are:
Container transform
Shared axis
Fade through
Fade

## how to select a patter?

Consider the following when determining which transition pattern is appropriate for a given use case.

### Persistent containers

Is a persistent container part of the transition?

Containers are shapes used to represent enclosed areas, such as buttons, lists, card surfaces. If there's a container that's a [persistent element](#) in the transition, choose the container transform pattern.

{% raw %}
<video src="https://storage.googleapis.com/spec-host/mio-staging%2Fmio-design%2F1581631970573%2Fassets%2F15TdN-zv3cqO5AUyS5SrS6I2tbgxSDFPv%2Fpick-container-transform-2col.mp4" controls="controls">
</video>
{% endraw %}

### Element types

[link](https://material.io/design/motion/choreography.html#sequencing)

UI components in a sequence are categorized as outgoing, incoming, persistent.
Outgoing elements exit the screen
Ongoing elements enter the screen.
Persistent elements start and end on screen.

In the demo, the persistent element(container) is blue; the outgoing element(list item content) is red; and the incoming element(details page content) is green.
{% raw %}
<video src="https://storage.cloud.google.com/non-spec-apps/mio-direct-embeds/videos/SEQUENCING%20ELEMENTS.mp4" controls="controls">
</video>
{% endraw %}

### Relationships

Is there a spatial or navigational relationship between UI elements?

If the elements in a transition do not have a persistent container, there may be a spatial relationship to emphasize, such as elements in a vertical or horizontal layout. Or, there might be a navigational relationship between elements, such as moving between peers or between levels in an app. If a spatial or navigational relationship exists between elements, choose the shared axis pattern.
{% raw %}
<video src="https://storage.googleapis.com/spec-host/mio-staging%2Fmio-design%2F1581631970573%2Fassets%2F1xyJ78lqkctwFliSv7mtBKhxZ_g4pjglh%2Fpick-shared-axis-2col.mp4" controls="controls">
</video>
{% endraw %}

When a relationship between UI components is insignificant or does not exist, the fade through pattern is better suited.

{% raw %}
<video src="https://storage.googleapis.com/spec-host/mio-staging%2Fmio-design%2F1581631970573%2Fassets%2F15Y38NqhiCSUX_Fh253WzhHLqZbsskk-g%2Fpick-fade-through-2col.mp4" controls="controls">
</video>
{% endraw %}

The fade through pattern are applied to UI components without a strong relationship to each other. Such as detinations in the bottom navigation.

### Entering and Exiting

Does a UI component enter or exit the screen?

Oftentimes a UI element simply needs to enter or exit the screen. For entering and exiting elements, the fade pattern is best suited.

{% raw %}
<video src="https://storage.googleapis.com/spec-host/mio-staging%2Fmio-design%2F1581631970573%2Fassets%2F15U7Uh31BRkOrM2RL7zDrDgM3dPrsU_Ue%2Fpick-fade-2col.mp4" controls="controls">
</video>
{% endraw %}

The fade pattern can be applied to UI element that enter or exit the screen, such as dialogs.
