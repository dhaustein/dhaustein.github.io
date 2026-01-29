+++
title = "How to Make Creative Speakers Not Suck"
description = "How to work around the automatic Standby mode on Creative speakers"
date = 2025-11-25
slug = "make-creative-speakers-not-suck"
[taxonomies]
tags = ["speakers", "hack", "windows"]
+++

### TLDR

I made a bat script that periodically sends a very high frequency sound to the speakers to prevent them from completely shutting off.

### The Problem

Many years ago, when my wife's old speakers finally went to silicon heaven, I got her brand new pair of my favourite speakers, the Creative T20.
I own a similar pair of a much older model. I have been very happy with them, and they still go strong for almost two decades now.
And being a happy owner of them, I recommended my wife gets the same albeit newer model as well.

Unfortunately for her, I did not do a more thorough research on the new version. You see, there is something called the [Energy-related Products Directive.](https://eur-lex.europa.eu/eli/dir/2009/125/oj/eng) In short, this directive encourages (forces?) manufacturers that are coming to the European market to offer products that are more energy efficient. The implementation of this directive is left to the manufacturer.

Creative in their effort to satisfy the regulation, added a Standby mode™ to their speakers. The speakers will essentially [shut down if they detect no input signal.](https://support.creative.com/kb/ShowArticle.aspx?sid=12873)

Again, this is very nice, the speakers will consume little to no energy when you don't play any sound. The problem, however, is that they take a second or two to wake up when they do have to play sound.

Imagine the following: You turn on your speakers, browse the web some, then you want to watch a Youtube video. The video starts, and you hear nothing. And then suddendly, the sounds comes back. But this is not a bug, this is a feature. The speakers simply went into Standby mode™ while you were doomcrolling. But them waking up from their slumber took a few moments of the video you wanted to watch, so now you have to restart it.

I admit this is a minor inconvenience, but annoying still. I had it ony my plate almost every day!

### The Solution

I wrote a short PowerShell script that constantly plays a sound of very high frequency. This is inperceptible to humans and it provides a constant signal to the speaker so that they wont go into Standby mode™.

Yes I know, this totally goes against any notion of trying to save energy. But the efect on the overall electricity consumption is tiny. The speakers are not turned on for most of the day anyway. The positive effect on the harmony of this household, however, has been off the charts.

Here is the code:

```powershell
# script.ps1
$PlayWav=New-Object System.Media.SoundPlayer

While ($true) {
    $PlayWav.SoundLocation="C:\example\path\tone.wav"
    $PlayWav.playsync()
    Start-Sleep -Seconds 1
}
```

This is very crude but it does the job. After you login to the desktop, the script will continuously play the audio file every second.
Which in turn will prevent the speakers from going into hibernation. I set it to 1 second and called it a day, but presumably a longer time interval should work too. It also depends on how long the audio file will be.

> [!NOTE]
> You likely notice this is written in PowerShell since it had to work on my wife's Windows. On Linux, you can use something like `ffplay` to generate an audio signal, or `paplay` to play your audio file.

Add the script to the Task scheduler and set it to trigger after login. The command the task runs should look something like this:

```
powershell -ExecutionPolicy Bypass -File C:\example\path\script.ps1
```

For the `tone.wav` audio file, you have options. You can donwload something from the Internet or you can generate the tone yourself. I used Audacity but anything similar will do. The important part is that you create a very high frequency sound with the amplitude as low as you can set it. That is, make the volume as low as possible. 

The sound will be imperceptible, but there is no point in blasting it at max. Presumably the speakers will not actually vibrate/do anything at these high frequencies, but I haven't done any actual measurements.

If it's any useful, here's what the tone I used looks like for reference:

{{ image(url="freq_analysis.png", no_hover=true, transparent=true) }}

And that's it! If you are more adventuours, [there are hardware modifications](https://imgur.com/gallery/creative-gigaworks-t20-rev-2-auto-off-removal-pwqiQHX) that do the same thing, but those are obviously more invasive.
