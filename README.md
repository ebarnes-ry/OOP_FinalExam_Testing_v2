# DJ-Audio-Application
DJ Application Player - Showcase of OOP concepts using C++ and the JUCE framework. 

I broke my final project’s setup into 3 main components: the Deck, the Mixer, and the Playlist. The Playlist includes functionalities pertaining to the loading, storing, and manipulating of track data that eventually get loaded into the decks for further manipulation under those functionalities. A user can load files in a few ways in my program, and this covers a few usability cases. Firstly, a user can select the “Load” button manually which will prompt a popup and a user can choose one or more tracks to be stored in the playlist this way. They can also simply drag and drop one or more files into the playlist, and they will automatically populate rows and columns of the playlist display “data frame” using the Juce table model. Additionally, and this pertains more to the DeckGUI component - though with functionality granted by playlist - the CUE button functions as both a “next” button to load tracks into respective deck playlists and a means to prompt for a file to be added directly from a user’s file system to the deck’s playlist. This way, when nothing is loaded into the playlist and someone attempts to skip to a new track, they can be prompted to add one this way. The playlist is also consequently responsible for handling buttons that add tracks to the respective decks and searching capabilities to find specific loaded tracks.
 The DeckGUI is the component that handles data specific to the track currently loaded from the playlist associated with it. Two instances of this are displayed, one for the left deck and one for the right. The deck objects provide a means to display the waveform, which is a visual representation of the track’s audio data, and playback position relative to this; ways to change the position in the track, which can be done either with the position slider- that represents an actual disc track with a label for the currently playing sound - or with the waveform itself, which has mouse interaction capabilities to set the relative position in the sound. The deck also can manipulate the speed of a loaded track with a slider, that points to a method in the DJAudioPlayer utility object. This utility is also responsible for playing and pausing functionalities in each deck and has an instance for each deck to allow tracks to be played and loaded/manipulated simultaneously. The more decks you add, the more sounds can be played at the same time as with a real analog application setup. And of course, the Deck is also where the CUE button feature lives, and its functionality as both a next button and a file loader set up by flags that can be retrieved by private methods in the class.
The two Deck objects are connected in different ways by the MixerComponent, which is a new component class I added to handle things that can be used to blend the two Deck sounds. This includes volume editing in different ways - manually for each respective deck’s audio, and with a crossfader that equalizes and sets each deck’s gain to relative values. This allows for seamless fading and mixing, as well as custom mixing as desired on the fly. The mixer also handles the Distortion capability that offsets the sounds of the tracks by different factors depending on the value of the slider. At 0 there’s no distortion, and there are different levels with different effects from there that depend on the sound itself. This can blend the two tracks in interesting ways when used in conjunction with volume fading.

 Description of how you went about customizing the user interface (R2) ◦
 The GUI of my application was my biggest learning curve. The first thing I had to tackle was understanding the layout of my application and how I would take these sketches from sketches into positions for each component and element. I ran into an issue further along while attempting to apply a Custom LookAndFeel to each component, which resulted in my development of a method that can set the component's size relative to its positions and other elements. I began by splitting the MainComponent’s window into the composite sections where each component would live, then splitting the components from there. I defined a column’s width and a row’s height, so each element would live as some factor of those - this also helped me to standardize things for easier editing. I knew I wanted to emulate the mirrored look of a traditional DJ deck, so to do this I implemented a simple condition that would set elements relative to either the left or right side of the screen depending on which deck the object represented. This is one area where object orientation was visible, positioning each element as relating to their parent element,

 as relating to the full component. I then applied different look and feel customs per component - deck, and mixer, as they would ultimately have varying looks. Splitting functionalities, such as drawing notches for a rotary custom, was very useful and helped to better layout processes for actually drawing things. This, I found, was crucial in the debugging process for the GUI, as many things can be volatile.
Research and technical information covering how you identified, analyzed, and implemented a new feature that you found in another DJ application (R3)
I love music. I’ve played classical music on varying levels all my life, and I have a lot of friends who are DJs since I also am very into concert and techno music scenes. Before this project, however, I barely had a concept of how an actual DJ-producing deck works (what silly buttons
 
 they press at the table in front of the room). I ended up asking my friend for some basic advice, and he sent me this Quora link with a description and outlined deck:
https://www.quora.com/What-do-each-of-the-buttons-and-knobs-on-a-DJ-machine-do
and a cool software that emulates the look and feel of actually producing with a deck:
https://www.algoriddim.com/djay-pro-mac
   Some features here that stuck out to me were the crossfader, manipulatable waveform display, and the accessible playlist functionality. This one incorporates iTunes/Apple Music, and many

 DJ setups use Spotify as a source for their tracks. I also took note of the frequency editing knobs that show up to give different effects to the sound. Having experience with guitar and mathematics, I did some musical projects in the past to create custom distortion effects on sound.
Playlist:
A table structure seemed suitable for the organization, Upon researching methods offered by JUCE, implementing their TableListBoxModel was appropriate to meet this requirement. Overriding their methods allowed me to customize the functionality of their row and paint displays. Starting off this, I defined the layout and structure using these methods, keeping in mind what each column would be holding and how they would be added/manipulated. Breaking down the process that the playlist would handle, I came up with some main goals: the display itself (handled by the box model), button click interactions, dragging and dropping files, searching the playlist, structuring the data, and a manual prompt load.
Beyond the box model, I also overrode the FileDragAndDropTarget behaviour in JUCE to allow my component to accept files dropped into it. Whenever that occurs, the data passed in is processed by local methods and stored in the storage system I defined. This system includes vector-based data structures to take input files and track titles, and “potential” files (files that can be added from the playlist to a deck) and titles that are what end up getting displayed in the rows. This system allows me to add and remove files smoothly without disrupting the flow of data, and when adding files to playlists - easy to access. Furthermore, this component also includes data processing members, such as members to get the length of a track file or adding manually to a deck’s playlist. The file search component was made possible by an overridden textEditor

 listener, that “filtered” contents of each potential track vector based on the values a user searches, and replaces the visible rows in the playlist with only these values matching search parameters. Object orientation techniques were integral to the implementation of this component since they facilitated customization, data management, user interaction, and data processing, resulting in a robust playlist management system that met my planning goals.
Waveform position:
An additional feature I added to the waveform was the ability to modify the position of the track relative to where a user interacts with the waveform display, including dragging to shift through smoothly. It almost functions as a version of the bar slider in JUCE, but with the waveform as a backdrop to is even more seamless. This is handled in the DeckGUI component, as it makes more sense to send the mouse interaction from there, where the position and player objects can be accessed. I set the overridden mouse event to be relative to the waveform display object and used the fact that thew waveform is set relative to the track position anyways, to then send that calculated relative position to the player, and update the additional positionSlider.
Distortion:
So, from my experience, distortion can be a bit of a rabbit hole so I decided to keep things simple. I wanted one “distortion effect” knob that can apply a general range of wobbly effects depending on the input sound. The method I used to achieve this was LFO(low-frequency oscillation) modulation - where I manipulated the buffer to create a dynamic distortion effect. Usually, I could split this into three sliders for LFO frequency, amplitude, and phase - but often for custom distortion, you can combine these aspects for a generalized knob that can achieve an

 interesting blend of effects. Making specific sliders is also challenging because there would have to be different handling for different types of audio input, which was irrelevant to the goal I had in mind with this functionality. In general, LFO edits these aspects:
Frequency Modulation-
- Increasing this speeds up the variation, lowering it makes smoother variation
- In general, LFOs generate low-frequency waveforms (such as sine, triangle, sawtooth, or
square waves) which have a much lower frequency compared to the audio signal they
modulate.
- The LFO frequency determines the speed of the oscillation
Amplitude Modulation-
- This controls the gain and intensity of the modulation
- As the LFO waveform oscillates, it alters the amplitude (volume) of the distorted signal
rhythmically or cyclically.
- low amp = subtle effect, high = intense
Phase modulation-
- less common, but can be used in a cool way to change the timing of the sounds
- changing when the modulation begins in relation to the original audio signal.
Combining these, as I would do when playing guitar with a custom distortion pedal that adds these customizations together for a desired effect - results in an interesting depth and shape to the sound in volume, timing, and clarity.
For my slider implementation, I ultimately added some methods to the player object - since this is where most of the editing of sound would be occurring. I added members for bases of each LFO level I was to edit, and an overall “distortion level” (0 being no distortion). The effect itself

 is created by modifying the getnextAudioBlock overridden method in my player that fills the buffer of frequencies for the source to ultimately output the sound. My distortion edits this buffer whenever the distortion is set to anything but “none”. Moving through the frequency spectrum, I calculate the sin wave of the initial LFO phase to get the starting oscillation, then skew the channel data by these values. The sample channel is ultimately set as a factor of the LFO amplitude and the value set on the slider, to offer varying tones. I then play around with the frequency and phase for each incoming sample, storing and distorting them serially as they come through. The final effect is a wobbly effect controlled and varied by how much or little “variation” you want in your final sound.
Crossfader:
 
1. Volume faders. These are level reduction faders that control the level of each channel (there are four, one for each channel.) If the fader is all the way open, the channel will play at original volume but the further down it is, the quieter each channel.
2. Crossfader. This fader is sideways and essentially is used purely for scratching. Each channel can be assigned to be cut out when the fader touches a specific end, allowing DJs to cut in and out between tracks rapidly for scratching
(QUORA PAGE)
Because the crossfader “combines” or “mixes” an aspect of each deck, I implement it in the Mixer Component. Object-oriented principles often dictate that functionality related to a specific object should be encapsulated within that object, and the Mixer Component serves this purpose perfectly. The two gains are accessed by the player, and they are set relative to each other as the fader method is activated. The slider in the center signifies the "even" sound or the point at which both decks have equal contribution to the audio output. Object-oriented principles encourage separating the slider value alteration and the method for crossfading itself, allowing for better organization and separation of concerns - which I accomplish here and for each slider and relative functionality. This slider is linked to the crossfader logi so that as it's moved, it adjusts the gains of the decks accordingly.
