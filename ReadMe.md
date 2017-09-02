### Sonic Pi Wav File Orchestra

#### Introduction
Hello, my name is Joe. I recently bought a Raspberry Pi and discovered the Sonic PI application.

I?m new to Sonic Pi and the Ruby language it is written in. 

Tip: When Sonic Pi complains about the syntax I?m trying to use, I use a search engine like Google to find the answer. Usually I search for:
Ruby convert number to string
The part after Ruby usually uses the words in the error message that Ruby displays. Most of the time, the question has already been asked and answered. 

So a lot of ideas are floating around my head that I?m itching to code. So here is just one of the ideas.

#### Wav files and where to find them

These are raw digital audio files without any data compression.
You may want to review the Sonic Pi help section on using external samples to see how to use a wav file to make sound. 
One way to get a sample is to plug a USB microphone into your computer and use software like Audacity to record the sound. Another way is to search the Internet.  Google Virtual Playing Orchestra  and you will find a link like this one

[Virtual Playing Orchestra]( http://virtualplaying.com/)

I downloaded and unzipped these files. There is a nest of folders, but the wav files are in the sub folders of the lib folder. The subfolders seem to identify where the sound was recorded. Going further down the directory tree you will find folders named after the instuments.

I used the trick in Explorer of selecting all the files, holding down the shift key and doing a right click and then selecting copy as path and then pasting the results, for instance here in this document. Another option is to paste the listing into Excel for editing

```
"C:\Users\jj\Documents\soundpkgs\Virtual-Playing-Orchestra-v2-01\Virtual-Playing-Orchestra2\libs\Iowa\samples\Trumpet-solo\Trumpet.novib.ff.A4-PB-loop.wav"
"C:\Users\jj\Documents\soundpkgs\Virtual-Playing-Orchestra-v2-01\Virtual-Playing-Orchestra2\libs\Iowa\samples\Trumpet-solo\Trumpet.novib.ff.A5-PB-loop.wav"
"C:\Users\jj\Documents\soundpkgs\Virtual-Playing-Orchestra-v2-01\Virtual-Playing-Orchestra2\libs\Iowa\samples\Trumpet-solo\Trumpet.novib.ff.B3-PB-loop.wav"
```
From Explorer you can click on the wav files and your computer will use the default audio player to play the file. The file names give you hints as to the content. For instance stc means staccato. C#4 means C sharp 4th octave.

That looks very promising until you realize that not all of the notes in the scale are there.

#### Using Sonic Pi to play a wav file

I created a directory and copied the file trumpets-sus-e4.wav into it. Does sus mean sustained?
The variable samps points to the directory in the following code that plays the file
```
samps = "c:/home/pi/samples/"
samps1=samps+"trumpets-sus-e4.wav"
sample samps1,rate: 1
sleep 1
```
If you change the rate value from 1, the pitch of the sound changes and the time of play changes. For Geezers like me it?s like changing the turntable speed, or the tape recorder speed.  Let?s investigate the change in pitch in the next section.

#### Pitch and the magic numbers
Again with the Google searching for frequency of musical notes
I found:
[Music note frequencies]( https://pages.mtu.edu/~suits/notefreqs.html)
I cut a portion of the table and pasted it into Excel and then did some ratios to get the magic numbers.
Ratio = Note Frequency/ Frequency of note E4

| Note | Freq | ratio | 
| ---: | ---: | ---: |
| C4 | 261.63 | 0.793708097 | 
|  C#4/Db4  | 277.18 | 0.840882201 |
| D4 | 293.66 | 0.890877651 |
|  D#4/Eb4  | 311.13 | 0.943876468 |
| E4 | 329.63 | 1 | 
| F4 | 349.23 | 1.059460607 |
|  F#4/Gb4  | 369.99 | 1.122440312 |
| G4 | 392 | 1.189212147 |
|  G#4/Ab4  | 415.3 | 1.259897461 |
| A4 | 440 | 1.334829961 |
|  A#4/Bb4  | 466.16 | 1.414191669 |
| B4 | 493.88 | 1.498285957 |
| C5 | 523.25 | 1.587385857  
| D5 | 587.32 | 1.781755301 |
| E5 | 622.26 | 1.887752935 |

Rework the table:

| Note | ratio|
| ---: | ---: |
| C | 0.793708097|
| CS | 0.840882201|
| DB | 0.840882201|
| D | 0.890877651|
| DS | 0.943876468|
| EB | 0.943876468|
| E | 1|
| F | 1.059460607|
| FS | 1.122440312|
| GB | 1.122440312|
| G | 1.189212147|
| GS | 1.259897461|
| AB | 1.259897461|
| A | 1.334829961|
| AS | 1.414191669|
| BB | 1.414191669|
| BB | 1.498285957|

What?s so magic about the ratio? Use them as the rate value to get the desired note. For instance to play note G4

```
samps = "c:/home/pi/samples/"
samps1=samps+"trumpets-sus-e4.wav"
sample samps1,rate: 1.189212147
sleep 1
```

#### But what about note duration and tempo?
Playing with the sample command parameters, this seems to work for me. But I have a tin ear, so let me know.

```
samps = "c:/home/pi/samples/"
samps1=samps+"trumpets-stc-rr2-e4.wav"
#samps1=samps+"cello-c4.wav"
samps1=samps+"trumpets-sus-e4.wav"
puts samps1
oct=0.793708097
st=1.0
live_loop :bar do
  sample samps1,rate:oct,attack: 0.1,
    sustain: st/2,release: st*2/3.0 #,pan: -0.8
  sleep st
  st=st/2.0
  if(st < 0.125) then st=1.0 end
  oct=oct*1.059460607
  if(oct>2) then oct=1.0 end
end
```





 
