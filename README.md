# test-repo
 My website
 
 ---
title: 'What typifies Kizomba music?'
output: 
  flexdashboard::flex_dashboard:
    storyboard: true
    theme: yeti
    orientation: columns
    vertical_layout: fill
    self_contained: false
    


```{r setup, include=FALSE, echo=FALSE}

library('ggplot2')
library('plotly')
library(usethis)
library(devtools)
library(spotifyr)
library(tidyverse)
library(knitr)
library(tidyr)
library(compmus)
Sys.setenv(SPOTIFY_CLIENT_ID = '9f3fa72cb3d948e496c93a9ea74ff94b')
Sys.setenv(SPOTIFY_CLIENT_SECRET = 'a7ecccc3b9d846f28d1636eacdbc94e2')

acces_token <- get_spotify_access_token()

knitr::opts_chunk$set(echo = TRUE)
```

```{r, echo=FALSE}
knitr::opts_chunk$set(error = TRUE)
```

# What typifies Kizomba music?

In this research will be given a frame to this new type of music called 'Kizomba'. It was created in the 70s/80s and means 'party' in Angolan. In this project is researched what is specific about kizomba music. We will use three recent made playlists of Kizomba, Bachata and Pop. All have been created and updated in the last two weeks. We will compare and look at the energylevels, modes, loudness and valence. 

Then we will look at three popular songs in more detail. Two versions of 'Sad Girlz Luv Money', the orginal and the kizomba remix version, and love nwantiti for CKay. 

For Sad Girlz Luv Money we will compare the original version and the kizomba version with chromagrams. In a chromagram is visualized what notes are used at what time. Here we can see what notes are used and if there is a difference between the original and the kizomba version. The self similairy matrix with chromafeatures will also give some insight about how consistant or contrastfull the song is. 

With 'love nwantiti' we will look at the the timbre features which show c01 to c12 and show two self-similarity matrix for chroma features, timbre features to see what timre features are used and how they are used throughout the song. 

# Differences in Scale, Valence, Energy and Loudness in 2022 tophits lists of Pop, Bachata and Kizomba

```{r = energy count 3 playlists, echo=FALSE}

kizombatophits <- get_playlist_audio_features("", "0Pk8kSKJ5vO5yIA36kzGMu?si=e846b8a12e834aea")
bachatatophits <- get_playlist_audio_features("", "3mmU6TpfA93BD13wZEMQdP?si=35116dac512743e7")
poptophits <- get_playlist_audio_features("", "05nCXZLYE5sgzonkzdNaP4?si=5c2b8c4d7b1c43a2")

tophits <-
  bind_rows(
    kizombatophits %>% mutate(category = "Kizomba"),
    bachatatophits %>% mutate(category = "Bachata"),
    poptophits %>% mutate(category = "Pop")
  )
  
  tophits %>%
  ggplot(aes(x = energy)) +
  geom_histogram(binwidth = 0.1) +
  facet_wrap(~category)


```


There are three playlists compared. All are from 2022 and have been recently currated. There are four variables shown in this graph from the genres bachata, kizomba and Pop. In the bachata playlist there are 130 songs, in the kizomba 125 songs and in the pop 160 songs. 

In the table here we can see the energy count. As we can see is the energy count in kizomba music very high in 0.65 to 0.75, then follows 0.55 to 0.65. A similar bar is shown in bachata. Energy levels higher then 0.75 are much lower in bachata. The value of 0.75 and higher are more represented in bachata and pop. 

From these graphs we can see that the energy value of kizomba is dominantly between 0.55 and 0.75. In bachata this is between 0.55 and 0.85, and for pop this is between 0.45 and 0.85. So typically, the energy level of kizomba often lies between 0.55 and 0.75, mostly between 0.65 and 0.75.

```{r tophits, echo=FALSE}


tophits %>%                    # Start with awards.
  mutate(
    mode = ifelse(mode == 0, "Minor", "Major")
  ) %>%
  ggplot(                     # Set up the plot.
    aes(
      x = valence,
      y = energy,
      size = loudness,
      colour = mode
    )
  ) +
  geom_point() +              # Scatter plot.
  geom_rug(size = 0.1) +      # Add 'fringes' to show data distribution.
  geom_text(                  # Add text labels from above.
    aes(
      x = valence,
      y = energy,
      label = label
    ),
    data = 
      tibble(
        label = c("", "ENERGY"),
        category = c("Kizomba", "Pop"),
        valence = c(0.090, 0.123),
        energy = c(0.101, 0.967)
      ),
    colour = "black",         # Override colour (not mode here).
    size = 3,                 # Override size (not loudness here).
    hjust = "left",           # Align left side of label with the point.
    vjust = "bottom",         # Align bottom of label with the point.
    nudge_x = -0.05,          # Nudge the label slightly left.
    nudge_y = 0.02            # Nudge the label slightly up.
  ) +
  facet_wrap(~category) +     # Separate charts per playlist.
  scale_x_continuous(         # Fine-tune the x axis.
    limits = c(0, 1),
    breaks = c(0, 0.50, 1),   # Use grid-lines for quadrants only.
    minor_breaks = NULL       # Remove 'minor' grid-lines.
  ) +
  scale_y_continuous(         # Fine-tune the y axis in the same way.
    limits = c(0, 1),
    breaks = c(0, 0.50, 1),
    minor_breaks = NULL
  ) +
  scale_colour_brewer(        # Use the Color Brewer to choose a palette.
    type = "qual",            # Qualitative set.
    palette = "Paired"        # Name of the palette is 'Paired'.
  ) +
  scale_size_continuous(      # Fine-tune the sizes of each point.
    trans = "exp",            # Use an exp transformation to emphasise loud.
    guide = "none"            # Remove the legend for size.
  ) +
  theme_light() +             # Use a simpler theme.
  labs(                       # Make the titles nice.
    x = "Valence",
    y = "Energy",
    colour = "Mode"
  )


```


### overall
Energy and valence both are presented in a scale from 0 to 1. The number of valence presents the 'hapiness' of the song and is shown in the X-axis, on the y-axis is shown the amount of energy, The colors show is the song is in minor or major. Major is colored lightblue and minor is colored dark blue. The size of the dots represents loudness. 

### Valence
We see that in the bachata graph the dots are places a lot to the right, which means overall the value of valence is very high. The valence of the kizomba songs is most placed between 0.3 and 0.7. With pop we see that all values of valence are represented. Close to zero as well as close to one. The Major scale is more used with a lower valence, and minor is more used with a higher valence, which is counter the idea that major songs are happy and minor scales are more sad songs. 

### Energy
In the kizomba graph we see that six songs, all in minor are below 0.5 in energy and the rest is above. In the bachata- and pop-playlists there are significantly more songs, especially in pop. As spoken about before most songs are between 0.55 and 0.75, specifically between 0.65 and 0.75. 

### Mode 
The modes seem quite evenly divided. although in pop we see that a lot of songs with low energy are in major, where in kizomba that was in minor. 

### loudness
The loudness in bachata songs is the highest as there the dots are bigger. Songs with higher value of valence and energy also show more louder songs. In the pop playlist the dots are small and there is not that big of a diffence, waht we can see is that the songs that are louder also are songs higher in energy. 


# Sad Girlz Luv Money Orginal and Kizomba version

Sad Girlz Luv Money has two versions. A pop version and a Kizomba remix with Khali Uchis. 

### sad girlz love money kizomba

```{r sad girlz kizomba chromagram, echo=FALSE}

sadgirlzkizomba <-
  get_tidy_audio_analysis("09gysnJpfQ3ublBmJDfcEC?si=5ea5c640ed424139") %>%
  select(segments) %>%
  unnest(segments) %>%
  select(start, duration, pitches)
  
  
sadgirlzkizomba %>%
  mutate(pitches = map(pitches, compmus_normalise, "euclidean")) %>%
  compmus_gather_chroma() %>% 
  ggplot(
    aes(
      x = start + duration / 2,
      width = duration,
      y = pitch_class,
      fill = value
    )
  ) +
  geom_tile() +
  labs(x = "Time (s)", y = NULL, fill = "Magnitude") +
  theme_minimal() +
  scale_fill_viridis_c()

```
Here we can see the chromagram of the kizomba remix. we see that C#/Db is represented quite dominantly throughout the song. C is represented more just after 50 seconds till 80 seconds and then again at 130 seconds, when C is more dominant we can also see some yellow peaks in F, which suggests a F chords without clear major or minor distinction. A and E have frequent highlights in green. The song ends with C#/Db, E and A in bright green. Together these notes form a A-major chords.

```{r sad girlz original chroma, echo=FALSE}

sadgirlzoriginal <-
  get_tidy_audio_analysis("2pgDBO0ZAgvye7joSmdDm8?si=2552a28412744719") %>%
  select(segments) %>%
  unnest(segments) %>%
  select(start, duration, pitches)


sadgirlzoriginal %>%
  mutate(pitches = map(pitches, compmus_normalise, "euclidean")) %>%
  compmus_gather_chroma() %>% 
  ggplot(
    aes(
      x = start + duration / 2,
      width = duration,
      y = pitch_class,
      fill = value
    )
  ) +
  geom_tile() +
  labs(x = "Time (s)", y = NULL, fill = "Magnitude") +
  theme_minimal() +
  scale_fill_viridis_c()  


```
In the original version we see that C lights up around 45 seconds. In the kizomba version, C goes dark blue and there are two clear highlighted parts. In the original song there is a less clear division. In F there is a similar pattern compared to the kizomba version. In F#/Gb there is a bright yellow part at 155 seconds that is not there in the kizomba version. There is a shorter bright green part at the end t G#/Ab. In the kizomba version there is around ten seconds of this dominantness of three bright green tones and in the original version we see around five seconds in one note. 


### Chroma self similarity matrix "Sad Girlz Luv Money' Kizomba version
```{r Sad Girlz Luv Money chroma kizomba, echo=FALSE}

  library(compmus)
  compmus_long_distance(
  sadgirlzkizomba %>% mutate(pitches = map(pitches, compmus_normalise, "chebyshev")),
  sadgirlzkizomba %>% mutate(pitches = map(pitches, compmus_normalise, "chebyshev")),
  feature = pitches,
  method = "manhattan"
) %>%
  ggplot(
    aes(
      x = xstart + xduration / 2,
      width = xduration,
      y = ystart + yduration / 2,
      height = yduration,
      fill = d
    )
  ) +
  geom_tile() +
  coord_equal() +
  labs(x = "sadgirlkizomba", y = "sadgirlzkizomba") +
  theme_minimal() +
  scale_fill_viridis_c(guide = NULL)
  
```


The patterns here in the self similarity matrix with chromafeatures is quite consistent. That is because the beat gives a strong pattern that is differentsounding then the melody of the song. So instead of a checkerboard pattern which is seen by the song of CKay, here there is a more subtle pattern with a lot of yellow lines.


# CKay timbre 

In this graph we see the magnitude of the effects. c01 to c06 is colored more yellow and green, which means the magnitude is higher. c07 to c12 iw colered bue, which means the magnitude is lower. Just before 50 seconds we see some bright yellow accents, and just before 100 seconds we see those accents again. When c01 is accented with a higher magnitude, the same goes for c03. The magnitude of c05 varies a lot throughout the song, it is not gradient as we see in c01, c02 and c04. It is a clear striped pattern. For this song we can say that timbre features from c01 to c06 are used a lot and c07 to c12 are not used that much. 


```{r ckay timbre, echo=FALSE}
ckay <- 
  get_tidy_audio_analysis("2Xr1dTzJee307rmrkt8c0g?si=4deadbb7d77042fc") %>% # Change URI.
  compmus_align(bars, segments) %>%                     # Change `bars`
  select(bars) %>%                                      #   in all three
  unnest(bars) %>%                                      #   of these lines.
  mutate(
    pitches =
      map(segments,
        compmus_summarise, pitches,
        method = "rms", norm = "euclidean"              # Change summary & norm.
      )
  ) %>%
  mutate(
    timbre =
      map(segments,
        compmus_summarise, timbre,
        method = "rms", norm = "euclidean"              # Change summary & norm.
      )
  )
  

  
  ckay %>%
  compmus_gather_timbre() %>%
  ggplot(
    aes(
      x = start + duration / 2,
      width = duration,
      y = basis,
      fill = value
    )
  ) +
  geom_tile() +
  labs(x = "Time (s)", y = NULL, fill = "Magnitude") +
  scale_fill_viridis_c() +                              
  theme_classic()

```

### timbre selfsimilarity matrix of pitches CKay 'love nwantiti'
  
  In the selfsimilarity matrix of the pitches of 'love nwantiti' we see a checkerboard of nine blocks when counting from leftunder to rightup. From 0 to 28 seconds there is a block with yellow around it. So we can see a change of pitches here. From 28 seconds to 64 seconds we see a block. At 64 seconds we see a light green cross that lasts for 5 seconds, a block of 10 seconds and then another cross that lasts five seconds. Parts were there is a lot of yellow show that there is a lot of difference from that seconds to the song to other parts of the song measured in time. After 90 seconds we see a big block and at 140 we see a lot of yellow again. Here we see that the intro and outro are very different from the rest of the song and in the song there are two moment close together as well that are very different in pitch to the rest of the song. In the next graph we will see the timbre.
  
  
```{r ckay self similarity, echo=FALSE}

  library(compmus)
  compmus_long_distance(
  ckay %>% mutate(pitches = map(pitches, compmus_normalise, "chebyshev")),
  ckay %>% mutate(pitches = map(pitches, compmus_normalise, "chebyshev")),
  feature = pitches,
  method = "manhattan"
) %>%
  ggplot(
    aes(
      x = xstart + xduration / 2,
      width = xduration,
      y = ystart + yduration / 2,
      height = yduration,
      fill = d
    )
  ) +
  geom_tile() +
  coord_equal() +
  labs(x = "", y = "") +
  theme_minimal() +
  scale_fill_viridis_c(guide = NULL)
  
```
 
### timbre selfsimilarity matrix of timbre of CKay 'love nwantiti'
  
  In the selfsimilarity matrix of the timbre of 'love nwantiti' we see that in the beginning there is more yellow. This means there is irregulairy in the timbre. the checkerboard figure shows how those parts are more similar to itself. Around 130 seconds there is a yellow cross. So in that part around 130 seconds there is a lot of difference with the song part. similarity is here with the beginning of the song.
   
```{r timbre self similarity, echo=FALSE}

  ckay %>%
  compmus_self_similarity(timbre, "cosine") %>% 
  ggplot(
    aes(
      x = xstart + xduration / 2,
      width = xduration,
      y = ystart + yduration / 2,
      height = yduration,
      fill = d
    )
  ) +
  geom_tile() +
  coord_fixed() +
  scale_fill_viridis_c(guide = "none") +
  theme_classic() +
  labs(x = "", y = "")

```





      








 
