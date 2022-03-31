---
title:  "ProPresenter behringer WING control"
---

# ProPresenter behringer WING control

## Dependencies

### [MidiPipe](http://www.subtlesoft.square7.net/MidiPipe.html)

### [ProPresenter 7](https://renewedvision.com/propresenter/)

### [behringer WING](https://www.behringer.com/product.html?modelCode=P0BV2)

- [MIDI Documentation](https://mediadl.musictribe.com/download/software/behringer/WING/WING_MIDI_Documentation.pdf)
- [DAW Documentation](https://mediadl.musictribe.com/download/software/behringer/WING/WING_DAW_Documentation.pdf)

## Theory

![DenverCoder9 - Wisdom of the Ancients](https://imgs.xkcd.com/comics/wisdom_of_the_ancients.png)

Thanks to [self replies by /u/glbltvlr](https://www.reddit.com/r/Behringer/comments/p529tv/wing_midi_control/) there are some breadcrumbs on how to get this done:

- Output standard midi notes from ProPresenter
- Use MidiPipe to convert to correct control change and channel
- Send the converted output to the WING

## Configuration

### MidiPipe

Create a virtual input and assign it to a MIDI input Tool
![MIDI In Virtual Input Wing](/assets/propresenter_wing_images/000_midi_in.png)

Add a Message Converter tool for Note On messages to Controller Change #000
![MIDI In Virtual Input Wing](/assets/propresenter_wing_images/001_message_converter.png)

Add a Message Converter tool for Note Off messages to Controller Change #000

This allows for the slider to easily be set to -Inf
![MIDI In Virtual Input Wing](/assets/propresenter_wing_images/002_message_converter.png)


Add a control split converting channels to specific controler channel combinations. 
![MIDI In Virtual Input Wing](/assets/propresenter_wing_images/003_control_split.png)

Repeat for each desired slider:
[Example 2](/assets/propresenter_wing_images/004_control_split.png)
[Example 3](/assets/propresenter_wing_images/005_control_split.png)
[Example 4](/assets/propresenter_wing_images/006_control_split.png)

The AList is a debugging tool for logging actions.
![AList](/assets/propresenter_wing_images/007_alist.png)

Set the midi output to the WING 4.
![MIDI In Virtual Input Wing](/assets/propresenter_wing_images/008_midi_out.png)

