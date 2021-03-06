### Sonic PI Wav File Orchestra

#### Introduction

The Sonic PI software creates musical sounds either from synthesisers or it can play various types of recorder sound files.
This project makes use of wav file recordings of musical instruments playing the E4 (E above middle C) note.
The pitch of the note is altered by playing the sound file at faster or slower rates.
The example program uses csv files to specify the octave, pitch and duration of the 4 instruments.

#### Where to download Sonic PI

The Sonic Pi software can be downloaded from
[Sonic PI](http://sonic-pi.net/)

#### Wav files and where to find them

These are raw digital audio files without any data compression.
You may want to review the Sonic Pi help section on using external samples to see how to use a wav file to make sound. 
One way to get a sample is to plug a USB microphone into your computer and use software like Audacity to record the sound. 
Another way is to search the Internet.  Google Virtual Playing Orchestra  and you will find a link like this one

[Virtual Playing Orchestra]( http://virtualplaying.com/)

Download and unzip these files. There is a nest of folders, but the wav files are in the sub folders of the lib folder. 
The subfolders seem to identify where the sound was recorded. 
Going further down the directory tree you will find folders named after the instuments.
From Explorer you can click on the wav files and your computer will use the default audio player to play the file. 
The file names give you hints as to the content. For instance stc means staccato. C#4 means C sharp 4th octave.
That looks very promising until you realize that not all of the notes in the scale are there.
For this project, copy the deisred instument files for the E4 note into one file folder

#### The Example Program O2J4P.txt

I created a directory and copied the file trumpets-sus-e4.wav into it.
The variable samps points to the directory in the following code.
samps = "c:/home/pi/samples/"
samps1=samps+"trumpets-sus-e4.wav"

The song is contained in four csv files.
songfile1="c:/home/songs/o2jP1.csv"
songfile2="c:/home/songs/o2jP2.csv"
songfile3="c:/home/songs/o2jP3.csv"
songfile4="c:/home/songs/o2jP4.csv"

You may need to edit the program to point to the porper file locations

The Sonic PI program is listed here:


```
# o2j4P.txt
# Live loop parameters
#tempo
# use st value to control the tempo
st=2.0 #slow tempo
st=1.5 #medium tempo
st=1.0 #fast tempo
# change keys by integer halftones +-
key1=0.0
# integer +- adds halftone to note number
m1=0.0
m2=0.0
m3=0.0
m4=0.0
# integer+- shifts octaves
octshift1 = 0.0
octshift2 = 0.0
octshift3 = 0.0
octshift4 = 0.0
# Amplitude values for each voice
amp1=1.0
amp2=0.25
amp3=0.25
amp4=0.25
# Pan values for each voice
pan1 =0.0
pan2 =1.0
pan3 =-1.0
pan4 =-1.0

#################################

require 'csv'
songfile1="c:/home/songs/o2jP1.csv"#read the csv file into an array of hashes
songfile2="c:/home/songs/o2jP2.csv"#read the csv file into an array of hashes
songfile3="c:/home/songs/o2jP3.csv"#read the csv file into an array of hashes
songfile4="c:/home/songs/o2jP4.csv"#read the csv file into an array of hashes
#read the csv file into an array of hashes
tune1 = Array.new
CSV.foreach(songfile1, { encoding: "UTF-8", headers: true, header_converters: :symbol, converters: :all}) do |row|
  tune1 << row.to_hash
end
tune2 = Array.new
CSV.foreach(songfile2, { encoding: "UTF-8", headers: true, header_converters: :symbol, converters: :all}) do |row|
  tune2 << row.to_hash
end
tune3 = Array.new
CSV.foreach(songfile2, { encoding: "UTF-8", headers: true, header_converters: :symbol, converters: :all}) do |row|
  tune3 << row.to_hash
end
tune4 = Array.new
CSV.foreach(songfile2, { encoding: "UTF-8", headers: true, header_converters: :symbol, converters: :all}) do |row|
  tune4 << row.to_hash
end
tune1=tune1.ring #convert to a ring datastructure
tune2=tune2.ring #convert to a ring datastructure
tune3=tune3.ring #convert to a ring datastructure
tune4=tune4.ring #convert to a ring datastructure

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
samps2=samps+"cello-c4.wav"
samps4=samps+"violin-e4.wav"
samps3=samps+"tuba-sus-e2.wav"
#samps3=samps+"oboe-e4-pb-loop.wav"


# The hash data structure that returns the note
# number
rates = {C:0.0,
         CS:1.0,
         DB:1.0,
         D:2.0,
         DS:3.0,
         EB:3.0,
         E:4.0,
         F:5.0,
         FS:6.0,
         GB:6.0,
         G:7.0,
         GS:8.0,
         AB:8.0,
         A:9.0,
         AS:10.0,
         BB:10.0,
         B:11.0,
         R:-100.0
         }


# one way to play wave files
halftone=Math.log(2.0)/12.0
octtone=Math.log(2.0)

define :playwavnote do |sampsn,octv,pitch,time,key,m,amp=1.0,pan=0.0|
  rate1=rates[pitch.upcase.intern]
  puts "ratenum=",rate1,key
  if(rate1< -99)
    sleep st*time.to_f
  else
    rate1=Math.exp((-4.0+rate1+m)*halftone+key*halftone+octv*octtone)
    puts "rate1=",rate1
    sample sampsn,
      rate:rate1,
      attack: 0.1,
      sustain: st*time.to_f*0.5,
      release: st*time.to_f*2.0/3.0,amp: amp ,pan: pan
sleep st*time.to_f
end
end

barcnt=0.0 #totals note length in a measure
barcount= 0.0 #counts measures
# set up a live loop to play the song
live_loop :bar1 do
  puts look
  note=tune1.tick
  unless note==nil
    puts note
    puts "key1=",key1
    puts note[:tone].intern
    playwavnote samps1,note[:octave]-4.0+octshift1,
      note[:tone],note[:time], key1,m1,amp1,pan1
    #test for a measure (barcount)
    # add up note times
    ## Caution don't use if == because of addition imprecision
    barcnt=barcnt+note[:time]
    puts "barcnt=",barcnt
    if(barcnt>=1.0) # 1.0 for 4:4 time 4/4=1 0.75 =3/4 or 3:4 time
      barcnt=0.0
      barcount += 1.0
      puts "barcount= ",barcount
    end
    if(barcount >= 16.0) #measures in the phrase
      key1+=4.0  # halftone to shift after each phrase
      if(key1>11.0)then key1=0 end
      barcount=0.0
    end
  else #end of song seen
    #tick_reset #causes it to play again
    sleep 0.1
  end
end

# set up a live loop to play the song
live_loop :bar2 do
  sync :bar1
  note=tune2.tick
  unless note==nil
    playwavnote samps1,note[:octave]-4.0+octshift2,note[:tone],
      note[:time], key1,m2,amp2,pan2
  else #end of song seen
    #tick_reset #causes it to play again
    sleep 0.1
  end
end

# set up a live loop to play the song
live_loop :bar3 do
  sync :bar1
  note=tune3.tick
  unless note==nil
    playwavnote samps1,note[:octave]-4.0+octshift3,
      note[:tone],note[:time], key1,m3,amp3,pan3
  else #end of song seen
    #tick_reset #causes it to play again
    sleep 0.1
  end
end

# set up a live loop to play the song
live_loop :bar4 do
  sync :bar1
  note=tune4.tick
  unless note==nil
    playwavnote samps1,note[:octave]-4.0+octshift4,
      note[:tone],note[:time], key1,m4,amp4,pan4
  else #end of song seen
    #tick_reset #causes it to play again
    sleep 0.1
  end
end
```
Here is a link to an MP2 file recording of the program
[Ode to Joy 4 voices with key shifting](
https://drive.google.com/file/d/0BxMOEsGLzwfeY01RZU1ISEJvNnc/view?usp=sharing)
 
