######ReadMe1.md

#### Using the magic numbers
Given a musical note, and a Wav file for a musical instrument based on the E note of a given ocatave, 
return the rate value that the Sonic Pi sample command will play the given note.
Start by defining a Ruby data structure called a hash

```
rates = {C:0.793708097,
         CS:0.840882201,
         DB:0.840882201,
         D:0.890877651,
         DS:0.943876468,
         EB:0.943876468,
         E:1,
         F:1.059460607,
         FS:1.122440312,
         GB:1.122440312,
         G:1.189212147,
         GS:1.259897461,
         AB:1.259897461,
         A:1.334829961,
         AS:1.414191669,
         BB:1.414191669,
         B:1.498285957,
         R:0.001
         }

puts rates[:CS]
puts rates[:G]
pitch = "D"
puts rates[pitch.intern]
pitch = "d"
puts rates[pitch.upcase.intern]
```

In SonicPi if you minimize the scope and run this code you will see the results in the right hand panel.

```
{run: 24, time: 0.0}
 +- 0.840882201
 +- 1.189212147
 +- 0.890877651
 +- 0.890877651
```

#### Make some sound
You will need to download the wav files as mentioned before for this to work.
Playwavnotes.txt and the Playwavenotes.mp2 implement this step.
Playwavenotes.txt listing:

```
# playwavnotes.txt
# you will need to download the wav files from
# http://virtualplaying.com
# unzip and copy selected files to a folder defined by
# the samps variable below

# use samps to point ot the folder where the wav files
# are located
samps = "c:/home/pi/samples/"
# setup several wav files using the #
# so you can live loop by deleting or adding the hashtags
samps1=samps+"trumpets-sus-e4.wav"
#samps1=samps+"tuba-sus-e2-PB-loop.wav"
samps1=samps+"cello-c4.wav"

# The hash data structure that translates a pitch
# to the rate value needed by the sample command
rates = {C:0.793708097,
         CS:0.840882201,
         DB:0.840882201,
         D:0.890877651,
         DS:0.943876468,
         EB:0.943876468,
         E:1,
         F:1.059460607,
         FS:1.122440312,
         GB:1.122440312,
         G:1.189212147,
         GS:1.259897461,
         AB:1.259897461,
         A:1.334829961,
         AS:1.414191669,
         BB:1.414191669,
         B:1.498285957,
         R:0.001
         }

# use st value to control the tempo
st=2.0 #slow tempo
st=1.0 #medium tempo
st=0.5 #fast tempo

# one way to play wave files
define :playwavnote do |sampsn,pitch,time|
  sample samps1,rate:rates[pitch.upcase.intern],
    attack: 0.1,
    sustain: st/2,release: st*2/3.0 #,pan: -0.8
  sleep st*time.to_f
end

# as with all live loops you can change notes and duration
# as the tune plays
live_loop :bar1 do
  playwavnote samps1,"C","1.0"
  playwavnote samps1,"g","0.5"
  playwavnote samps1,"E","0.5"
  playwavnote samps1,"F","0.5"
  playwavnote samps1,"G","0.5"
  playwavnote samps1,"a","0.5"
  playwavnote samps1,"b","0.5"
  playwavnote samps1,"C","1.0"
end
```
Have some live looping fun by using hashtags to change instuments or changing up the notes, Try a "Gs" or a "Gb" or a "r".
Try changing the length of the notes. Write a new tune. Change the st tempo value.
Notice that you can only play the notes in one octave, which will be addressed as this project advances.
