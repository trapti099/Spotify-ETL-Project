```python
!pip install spotipy
```

    Requirement already satisfied: spotipy in c:\users\trapt\anaconda3\lib\site-packages (2.23.0)
    Requirement already satisfied: redis>=3.5.3 in c:\users\trapt\anaconda3\lib\site-packages (from spotipy) (5.0.1)
    Requirement already satisfied: requests>=2.25.0 in c:\users\trapt\anaconda3\lib\site-packages (from spotipy) (2.28.1)
    Requirement already satisfied: six>=1.15.0 in c:\users\trapt\anaconda3\lib\site-packages (from spotipy) (1.16.0)
    Requirement already satisfied: urllib3>=1.26.0 in c:\users\trapt\anaconda3\lib\site-packages (from spotipy) (1.26.11)
    Requirement already satisfied: async-timeout>=4.0.2 in c:\users\trapt\anaconda3\lib\site-packages (from redis>=3.5.3->spotipy) (4.0.3)
    Requirement already satisfied: charset-normalizer<3,>=2 in c:\users\trapt\anaconda3\lib\site-packages (from requests>=2.25.0->spotipy) (2.0.4)
    Requirement already satisfied: idna<4,>=2.5 in c:\users\trapt\anaconda3\lib\site-packages (from requests>=2.25.0->spotipy) (3.3)
    Requirement already satisfied: certifi>=2017.4.17 in c:\users\trapt\anaconda3\lib\site-packages (from requests>=2.25.0->spotipy) (2023.7.22)
    


```python
import spotipy
from spotipy.oauth2 import SpotifyClientCredentials
```


```python
import pandas as pd
```


```python
client_credentials_manager=SpotifyClientCredentials(client_id='11c94e6a7a234ab7af44488255012d67', client_secret='4d6c0f2a3301425cb342ef0f5035cc2f')
```


```python
sp = spotipy.Spotify(client_credentials_manager=client_credentials_manager)
```


```python
playlist_link = "https://open.spotify.com/playlist/37i9dQZEVXbMDoHDwVN2tF"
```


```python
playlist_link.split('/')
```




    ['https:', '', 'open.spotify.com', 'playlist', '37i9dQZEVXbMDoHDwVN2tF']




```python
playlist_URI = playlist_link.split('/')[4]
playlist_URI
```




    '37i9dQZEVXbMDoHDwVN2tF'




```python
playlist_data= sp.playlist_tracks(playlist_URI)
```


```python
# Initialize dictionaries to store track, album, and artist data
track_data_dict = {
    'Track Name': [],
    'Artist(s) Name': [],
    'Album Name': [],
    'Release Date': [],
    'Popularity Score': [],
    'Track URL':[]
}

album_data_dict = {
    'Album Name': [],
    'Artist(s) Name': [],
    'Release Date': [],
    'Total Tracks': [],
    'Cover Art URL':[]
}

```


```python
# Extract data from the playlist
for track in playlist_data['items']:
    # Track data
    track_data = track['track']
    track_data_dict['Track Name'].append(track_data['name'])
    track_data_dict['Artist(s) Name'].append(', '.join([artist['name'] for artist in track_data['artists']]))
    track_data_dict['Album Name'].append(track_data['album']['name'])
    track_data_dict['Popularity Score'].append(track_data['popularity'])
    track_data_dict['Release Date'].append(track_data['album']['release_date'])
    
    # External URL for the track
    external_urls = track_data.get('external_urls', {})
    track_url = external_urls.get('spotify')
    track_data_dict['Track URL'].append(track_url)
    

    # Album data
    album_data_dict['Album Name'].append(track_data['album']['name'])
    album_data_dict['Artist(s) Name'].append(', '.join([artist['name'] for artist in track_data['artists']]))
    album_data_dict['Release Date'].append(track_data['album']['release_date'])
    album_data_dict['Total Tracks'].append(track_data['album']['total_tracks'])
    
    # Cover Art URL (You can choose different image sizes)
    cover_art = track_data['album']['images'][0]['url'] if track_data['album']['images'] else None
    album_data_dict['Cover Art URL'].append(cover_art)
```


```python
# Create dataframes from the dictionaries
track_df = pd.DataFrame(track_data_dict)
album_df = pd.DataFrame(album_data_dict)
```


```python
# Print the first few rows of each dataframe
print("Track Data:")
print(track_df.head())

print("\nAlbum Data:")
print(album_df.head())

```

    Track Data:
                                Track Name          Artist(s) Name  \
    0                   Paint The Town Red                Doja Cat   
    1  Seven (feat. Latto) (Explicit Ver.)        Jung Kook, Latto   
    2               3D (feat. Jack Harlow)  Jung Kook, Jack Harlow   
    3                               greedy              Tate McRae   
    4                         Cruel Summer            Taylor Swift   
    
                   Album Name Release Date  Popularity Score  \
    0                 Scarlet   2023-09-20                77   
    1     Seven (feat. Latto)   2023-07-14                97   
    2  3D (feat. Jack Harlow)   2023-09-29                 0   
    3                  greedy   2023-09-15                91   
    4                   Lover   2019-08-23                98   
    
                                               Track URL  
    0  https://open.spotify.com/track/56y1jOTK0XSvJzV...  
    1  https://open.spotify.com/track/7x9aauaA9cu6tyf...  
    2  https://open.spotify.com/track/6ehWdR7cGDXnT7a...  
    3  https://open.spotify.com/track/3rUGC1vUpkDG9CZ...  
    4  https://open.spotify.com/track/1BxfuPKGuaTgP7a...  
    
    Album Data:
                   Album Name          Artist(s) Name Release Date  Total Tracks  \
    0                 Scarlet                Doja Cat   2023-09-20            15   
    1     Seven (feat. Latto)        Jung Kook, Latto   2023-07-14             3   
    2  3D (feat. Jack Harlow)  Jung Kook, Jack Harlow   2023-09-29             3   
    3                  greedy              Tate McRae   2023-09-15             1   
    4                   Lover            Taylor Swift   2019-08-23            18   
    
                                           Cover Art URL  
    0  https://i.scdn.co/image/ab67616d0000b273a84003...  
    1  https://i.scdn.co/image/ab67616d0000b273bf5cce...  
    2  https://i.scdn.co/image/ab67616d0000b273409a2d...  
    3  https://i.scdn.co/image/ab67616d0000b27322fd80...  
    4  https://i.scdn.co/image/ab67616d0000b273e787cf...  
    


```python
track_df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 50 entries, 0 to 49
    Data columns (total 6 columns):
     #   Column            Non-Null Count  Dtype 
    ---  ------            --------------  ----- 
     0   Track Name        50 non-null     object
     1   Artist(s) Name    50 non-null     object
     2   Album Name        50 non-null     object
     3   Release Date      50 non-null     object
     4   Popularity Score  50 non-null     int64 
     5   Track URL         50 non-null     object
    dtypes: int64(1), object(5)
    memory usage: 2.5+ KB
    


```python
# Convert the "Release Date" column to datetime
track_df['Release Date'] = pd.to_datetime(track_df['Release Date'])

# Print the first few rows of the DataFrame to verify the changes
print(track_df.head())


# Similarly


# Convert the "Release Date" column to datetime
album_df['Release Date'] = pd.to_datetime(album_df['Release Date'])

# Print the first few rows of the DataFrame to verify the changes
print(album_df.head())

```

                                Track Name          Artist(s) Name  \
    0                   Paint The Town Red                Doja Cat   
    1  Seven (feat. Latto) (Explicit Ver.)        Jung Kook, Latto   
    2               3D (feat. Jack Harlow)  Jung Kook, Jack Harlow   
    3                               greedy              Tate McRae   
    4                         Cruel Summer            Taylor Swift   
    
                   Album Name Release Date  Popularity Score  \
    0                 Scarlet   2023-09-20                77   
    1     Seven (feat. Latto)   2023-07-14                97   
    2  3D (feat. Jack Harlow)   2023-09-29                 0   
    3                  greedy   2023-09-15                91   
    4                   Lover   2019-08-23                98   
    
                                               Track URL  
    0  https://open.spotify.com/track/56y1jOTK0XSvJzV...  
    1  https://open.spotify.com/track/7x9aauaA9cu6tyf...  
    2  https://open.spotify.com/track/6ehWdR7cGDXnT7a...  
    3  https://open.spotify.com/track/3rUGC1vUpkDG9CZ...  
    4  https://open.spotify.com/track/1BxfuPKGuaTgP7a...  
                   Album Name          Artist(s) Name Release Date  Total Tracks  \
    0                 Scarlet                Doja Cat   2023-09-20            15   
    1     Seven (feat. Latto)        Jung Kook, Latto   2023-07-14             3   
    2  3D (feat. Jack Harlow)  Jung Kook, Jack Harlow   2023-09-29             3   
    3                  greedy              Tate McRae   2023-09-15             1   
    4                   Lover            Taylor Swift   2019-08-23            18   
    
                                           Cover Art URL  
    0  https://i.scdn.co/image/ab67616d0000b273a84003...  
    1  https://i.scdn.co/image/ab67616d0000b273bf5cce...  
    2  https://i.scdn.co/image/ab67616d0000b273409a2d...  
    3  https://i.scdn.co/image/ab67616d0000b27322fd80...  
    4  https://i.scdn.co/image/ab67616d0000b273e787cf...  
    


```python
track_df.info()
album_df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 50 entries, 0 to 49
    Data columns (total 6 columns):
     #   Column            Non-Null Count  Dtype         
    ---  ------            --------------  -----         
     0   Track Name        50 non-null     object        
     1   Artist(s) Name    50 non-null     object        
     2   Album Name        50 non-null     object        
     3   Release Date      50 non-null     datetime64[ns]
     4   Popularity Score  50 non-null     int64         
     5   Track URL         50 non-null     object        
    dtypes: datetime64[ns](1), int64(1), object(4)
    memory usage: 2.5+ KB
    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 50 entries, 0 to 49
    Data columns (total 5 columns):
     #   Column          Non-Null Count  Dtype         
    ---  ------          --------------  -----         
     0   Album Name      50 non-null     object        
     1   Artist(s) Name  50 non-null     object        
     2   Release Date    50 non-null     datetime64[ns]
     3   Total Tracks    50 non-null     int64         
     4   Cover Art URL   50 non-null     object        
    dtypes: datetime64[ns](1), int64(1), object(3)
    memory usage: 2.1+ KB
    


```python
track_df.to_csv(r"C:\Users\trapt\Downloads\Track.csv", index=False)
album_df.to_csv(r"C:\Users\trapt\Downloads\Album.csv", index=False)
```


```python
# Merge the DataFrames based on common columns
merged_df = pd.merge(track_df, album_df, on=['Artist(s) Name', 'Album Name', 'Release Date'], how='inner')
```


```python
print(merged_df)
```

                                 Track Name              Artist(s) Name  \
    0                    Paint The Town Red                    Doja Cat   
    1                    Paint The Town Red                    Doja Cat   
    2                           Agora Hills                    Doja Cat   
    3                           Agora Hills                    Doja Cat   
    4   Seven (feat. Latto) (Explicit Ver.)            Jung Kook, Latto   
    ..                                  ...                         ...   
    57                            Super Shy                    NewJeans   
    58                      La Bebe - Remix       Yng Lvcas, Peso Pluma   
    59                      Ella Baila Sola  Eslabon Armado, Peso Pluma   
    60                             I KNOW ?                Travis Scott   
    61        Calm Down (with Selena Gomez)          Rema, Selena Gomez   
    
                  Album Name Release Date  Popularity Score  \
    0                Scarlet   2023-09-20                77   
    1                Scarlet   2023-09-20                77   
    2                Scarlet   2023-09-20                68   
    3                Scarlet   2023-09-20                68   
    4    Seven (feat. Latto)   2023-07-14                97   
    ..                   ...          ...               ...   
    57  NewJeans 'Super Shy'   2023-07-07                92   
    58       La Bebe (Remix)   2023-03-17                93   
    59             DESVELADO   2023-04-28                86   
    60                UTOPIA   2023-07-28                92   
    61    Rave & Roses Ultra   2023-04-27                80   
    
                                                Track URL  Total Tracks  \
    0   https://open.spotify.com/track/56y1jOTK0XSvJzV...            15   
    1   https://open.spotify.com/track/56y1jOTK0XSvJzV...            15   
    2   https://open.spotify.com/track/5PyDJG7SQRgWXef...            15   
    3   https://open.spotify.com/track/5PyDJG7SQRgWXef...            15   
    4   https://open.spotify.com/track/7x9aauaA9cu6tyf...             3   
    ..                                                ...           ...   
    57  https://open.spotify.com/track/5sdQOyqq2IDhvmx...             2   
    58  https://open.spotify.com/track/2UW7JaomAMuX9pZ...             2   
    59  https://open.spotify.com/track/3qQbCzHBycnDpGs...            16   
    60  https://open.spotify.com/track/6wsqVwoiVH2kde4...            19   
    61  https://open.spotify.com/track/1s7oOCT8vauUh01...            22   
    
                                            Cover Art URL  
    0   https://i.scdn.co/image/ab67616d0000b273a84003...  
    1   https://i.scdn.co/image/ab67616d0000b273a84003...  
    2   https://i.scdn.co/image/ab67616d0000b273a84003...  
    3   https://i.scdn.co/image/ab67616d0000b273a84003...  
    4   https://i.scdn.co/image/ab67616d0000b273bf5cce...  
    ..                                                ...  
    57  https://i.scdn.co/image/ab67616d0000b2733d98a0...  
    58  https://i.scdn.co/image/ab67616d0000b273a04be3...  
    59  https://i.scdn.co/image/ab67616d0000b273dfddf1...  
    60  https://i.scdn.co/image/ab67616d0000b273881d8d...  
    61  https://i.scdn.co/image/ab67616d0000b273963265...  
    
    [62 rows x 8 columns]
    


```python
# Remove duplicate rows based on 'Track Name' column
merged_df = merged_df.drop_duplicates(subset=['Track Name'])

# Display the DataFrame without duplicates
print(merged_df)

```

                                               Track Name  \
    0                                  Paint The Town Red   
    2                                         Agora Hills   
    4                 Seven (feat. Latto) (Explicit Ver.)   
    5                              3D (feat. Jack Harlow)   
    7             3D (feat. Jack Harlow) (Alternate Ver.)   
    9                                              greedy   
    10                                       Cruel Summer   
    11                                          Strangers   
    12                                         UN PREVIEW   
    13                                              QLONA   
    14                                               LALA   
    15                                            vampire   
    18                                    bad idea right?   
    21                                      get him back!   
    24                                           Columbia   
    25                                           fukumean   
    26                                          LADY GAGA   
    27            Dance The Night - From Barbie The Album   
    28  What Was I Made For? [From The Motion Picture ...   
    29                                              Prada   
    30                                   I Wanna Be Yours   
    31                                           Daylight   
    32                                          As It Was   
    33                                        Según Quién   
    34                                        Si No Estás   
    35                                            Starboy   
    36                                           Que Onda   
    37                                          Kill Bill   
    38                                         Classy 101   
    39      I Remember Everything (feat. Kacey Musgraves)   
    40                                       Primera Cita   
    41                                       Slow Dancing   
    42                                           Sprinter   
    43                                            Chaleya   
    44                                            Holanda   
    45                                        Blank Space   
    47                                              Style   
    49                                        Die For You   
    50                               Locked out of Heaven   
    51  Popular (with Playboi Carti & Madonna) - Music...   
    52                                          un x100to   
    53                                            Flowers   
    54                                       Another Love   
    55                                    Sweater Weather   
    56                                          Anti-Hero   
    57                                          Super Shy   
    58                                    La Bebe - Remix   
    59                                    Ella Baila Sola   
    60                                           I KNOW ?   
    61                      Calm Down (with Selena Gomez)   
    
                                           Artist(s) Name  \
    0                                            Doja Cat   
    2                                            Doja Cat   
    4                                    Jung Kook, Latto   
    5                              Jung Kook, Jack Harlow   
    7                              Jung Kook, Jack Harlow   
    9                                          Tate McRae   
    10                                       Taylor Swift   
    11                                        Kenya Grace   
    12                                          Bad Bunny   
    13                                KAROL G, Peso Pluma   
    14                                        Myke Towers   
    15                                     Olivia Rodrigo   
    18                                     Olivia Rodrigo   
    21                                     Olivia Rodrigo   
    24                                            Quevedo   
    25                                              Gunna   
    26           Peso Pluma, Gabito Ballesteros, Junior H   
    27                                           Dua Lipa   
    28                                      Billie Eilish   
    29                        cassö, RAYE, D-Block Europe   
    30                                     Arctic Monkeys   
    31                                      David Kushner   
    32                                       Harry Styles   
    33                                 Maluma, Carin Leon   
    34                                     iñigo quintero   
    35                              The Weeknd, Daft Punk   
    36               Calle 24, Chino Pacas, Fuerza Regida   
    37                                                SZA   
    38                                   Feid, Young Miko   
    39                        Zach Bryan, Kacey Musgraves   
    40                                         Carin Leon   
    41                                                  V   
    42                                  Dave, Central Cee   
    43  Anirudh Ravichander, Arijit Singh, Shilpa Rao,...   
    44                                             Jhayco   
    45                                       Taylor Swift   
    47                                       Taylor Swift   
    49                                         The Weeknd   
    50                                         Bruno Mars   
    51                 The Weeknd, Playboi Carti, Madonna   
    52                          Grupo Frontera, Bad Bunny   
    53                                        Miley Cyrus   
    54                                          Tom Odell   
    55                                  The Neighbourhood   
    56                                       Taylor Swift   
    57                                           NewJeans   
    58                              Yng Lvcas, Peso Pluma   
    59                         Eslabon Armado, Peso Pluma   
    60                                       Travis Scott   
    61                                 Rema, Selena Gomez   
    
                                               Album Name Release Date  \
    0                                             Scarlet   2023-09-20   
    2                                             Scarlet   2023-09-20   
    4                                 Seven (feat. Latto)   2023-07-14   
    5                              3D (feat. Jack Harlow)   2023-09-29   
    7                              3D (feat. Jack Harlow)   2023-09-29   
    9                                              greedy   2023-09-15   
    10                                              Lover   2019-08-23   
    11                                          Strangers   2023-09-01   
    12                                         UN PREVIEW   2023-09-25   
    13                MAÑANA SERÁ BONITO (BICHOTA SEASON)   2023-08-10   
    14                                     LA VIDA ES UNA   2023-03-23   
    15                                               GUTS   2023-09-08   
    18                                               GUTS   2023-09-08   
    21                                               GUTS   2023-09-08   
    24                                           Columbia   2023-07-07   
    25                                   a Gift & a Curse   2023-06-16   
    26                                            GÉNESIS   2023-06-29   
    27            Dance The Night (From Barbie The Album)   2023-05-25   
    28  What Was I Made For? [From The Motion Picture ...   2023-07-13   
    29                                              Prada   2023-08-11   
    30                                                 AM   2013-09-09   
    31                                           Daylight   2023-04-14   
    32                                      Harry's House   2022-05-20   
    33                                           Don Juan   2023-08-25   
    34                                        Si No Estás   2022-09-23   
    35                                            Starboy   2016-11-25   
    36                                           Que Onda   2023-08-30   
    37                                                SOS   2022-12-08   
    38                                         Classy 101   2023-03-31   
    39                                         Zach Bryan   2023-08-25   
    40                                  Colmillo De Leche   2023-05-19   
    41                                            Layover   2023-09-08   
    42                                           Sprinter   2023-06-01   
    43                                              Jawan   2023-09-05   
    44                                            Holanda   2023-07-28   
    45                                               1989   2014-10-27   
    47                                               1989   2014-10-27   
    49                                            Starboy   2016-11-24   
    50                                 Unorthodox Jukebox   2012-12-07   
    51       Popular (Music from the HBO Original Series)   2023-06-02   
    52                                        El Comienzo   2023-08-25   
    53                            Endless Summer Vacation   2023-08-18   
    54                             Long Way Down (Deluxe)   2013-06-24   
    55                                        I Love You.   2013-04-19   
    56                                          Midnights   2022-10-21   
    57                               NewJeans 'Super Shy'   2023-07-07   
    58                                    La Bebe (Remix)   2023-03-17   
    59                                          DESVELADO   2023-04-28   
    60                                             UTOPIA   2023-07-28   
    61                                 Rave & Roses Ultra   2023-04-27   
    
        Popularity Score                                          Track URL  \
    0                 77  https://open.spotify.com/track/56y1jOTK0XSvJzV...   
    2                 68  https://open.spotify.com/track/5PyDJG7SQRgWXef...   
    4                 97  https://open.spotify.com/track/7x9aauaA9cu6tyf...   
    5                  0  https://open.spotify.com/track/6ehWdR7cGDXnT7a...   
    7                  0  https://open.spotify.com/track/2pLZ6tUBapqlngc...   
    9                 91  https://open.spotify.com/track/3rUGC1vUpkDG9CZ...   
    10                98  https://open.spotify.com/track/1BxfuPKGuaTgP7a...   
    11                94  https://open.spotify.com/track/5mjYQaktjmjcMKc...   
    12                85  https://open.spotify.com/track/63ui2w3QJBp8xx0...   
    13                92  https://open.spotify.com/track/5RqSsdzTNPX1uzk...   
    14                97  https://open.spotify.com/track/7ABLbnD53cQK00m...   
    15                94  https://open.spotify.com/track/1kuGVB7EU95pJOb...   
    18                92  https://open.spotify.com/track/3IX0yuEVvDbnqUw...   
    21                92  https://open.spotify.com/track/2gyxAWHebV7xPYV...   
    24                95  https://open.spotify.com/track/6XbtvPmIpyCbjuT...   
    25                95  https://open.spotify.com/track/4rXLjWdF2ZZpXCV...   
    26                94  https://open.spotify.com/track/7mXuWTczZNxG5ED...   
    27                95  https://open.spotify.com/track/1vYXt7VSjH9JIM5...   
    28                96  https://open.spotify.com/track/6wf7Yu7cxBSPrRl...   
    29                92  https://open.spotify.com/track/59NraMJsLaMCVtw...   
    30                94  https://open.spotify.com/track/5XeFesFbtLpXzIV...   
    31                94  https://open.spotify.com/track/1odExI7RdWc4BT5...   
    32                93  https://open.spotify.com/track/4Dvkj6JhhA12EX0...   
    33                81  https://open.spotify.com/track/4bw8mcDUSRWfQo6...   
    34                79  https://open.spotify.com/track/2HafqoJbgXdtjwC...   
    35                92  https://open.spotify.com/track/7MXVkk9YMctZqd1...   
    36                90  https://open.spotify.com/track/6uIIdjYTxxpWOyW...   
    37                91  https://open.spotify.com/track/1Qrg8KqiBpW07V7...   
    38                93  https://open.spotify.com/track/6XSqqQIy7Lm7Snw...   
    39                92  https://open.spotify.com/track/4KULAymBBJcPRpk...   
    40                86  https://open.spotify.com/track/25OeKzcqakFPaJl...   
    41                94  https://open.spotify.com/track/5h1BN75CEh8wdSw...   
    42                93  https://open.spotify.com/track/2FDTHlrBguDzQkp...   
    43                80  https://open.spotify.com/track/3xMHXmedL5Rvfxm...   
    44                92  https://open.spotify.com/track/6irysuQyZWd7Bjj...   
    45                81  https://open.spotify.com/track/1p80LdxRV74UKvL...   
    47                81  https://open.spotify.com/track/4lIxdJw6W3Fg4vU...   
    49                89  https://open.spotify.com/track/2LBqCSwhJGcFQeT...   
    50                90  https://open.spotify.com/track/3w3y8KPTfNeOKPi...   
    51                92  https://open.spotify.com/track/6WzRpISELf3YglG...   
    52                83  https://open.spotify.com/track/2yzshFeBIwH8tWI...   
    53                75  https://open.spotify.com/track/7DSAEUvxU8FajXt...   
    54                90  https://open.spotify.com/track/3JvKfv6T31zO0in...   
    55                91  https://open.spotify.com/track/2QjOHCTQ1Jl3zaw...   
    56                92  https://open.spotify.com/track/0V3wPSX9ygBnCm8...   
    57                92  https://open.spotify.com/track/5sdQOyqq2IDhvmx...   
    58                93  https://open.spotify.com/track/2UW7JaomAMuX9pZ...   
    59                86  https://open.spotify.com/track/3qQbCzHBycnDpGs...   
    60                92  https://open.spotify.com/track/6wsqVwoiVH2kde4...   
    61                80  https://open.spotify.com/track/1s7oOCT8vauUh01...   
    
        Total Tracks                                      Cover Art URL  
    0             15  https://i.scdn.co/image/ab67616d0000b273a84003...  
    2             15  https://i.scdn.co/image/ab67616d0000b273a84003...  
    4              3  https://i.scdn.co/image/ab67616d0000b273bf5cce...  
    5              3  https://i.scdn.co/image/ab67616d0000b273409a2d...  
    7              3  https://i.scdn.co/image/ab67616d0000b273409a2d...  
    9              1  https://i.scdn.co/image/ab67616d0000b27322fd80...  
    10            18  https://i.scdn.co/image/ab67616d0000b273e787cf...  
    11             1  https://i.scdn.co/image/ab67616d0000b2734756c2...  
    12             1  https://i.scdn.co/image/ab67616d0000b273c1156c...  
    13             9  https://i.scdn.co/image/ab67616d0000b273d026bf...  
    14            23  https://i.scdn.co/image/ab67616d0000b2730656d5...  
    15            12  https://i.scdn.co/image/ab67616d0000b273e85259...  
    18            12  https://i.scdn.co/image/ab67616d0000b273e85259...  
    21            12  https://i.scdn.co/image/ab67616d0000b273e85259...  
    24             1  https://i.scdn.co/image/ab67616d0000b273a00a81...  
    25            15  https://i.scdn.co/image/ab67616d0000b273017d5e...  
    26            17  https://i.scdn.co/image/ab67616d0000b2732fb583...  
    27             1  https://i.scdn.co/image/ab67616d0000b2737dd3ba...  
    28             1  https://i.scdn.co/image/ab67616d0000b273ed317e...  
    29             1  https://i.scdn.co/image/ab67616d0000b273e4b7dc...  
    30            12  https://i.scdn.co/image/ab67616d0000b2734ae1c4...  
    31             1  https://i.scdn.co/image/ab67616d0000b27395ca6a...  
    32            13  https://i.scdn.co/image/ab67616d0000b2732e8ed7...  
    33            25  https://i.scdn.co/image/ab67616d0000b273d688b9...  
    34             1  https://i.scdn.co/image/ab67616d0000b2735cb953...  
    35            18  https://i.scdn.co/image/ab67616d0000b2734718e2...  
    36             1  https://i.scdn.co/image/ab67616d0000b273c069ab...  
    37            23  https://i.scdn.co/image/ab67616d0000b2730c471c...  
    38             1  https://i.scdn.co/image/ab67616d0000b27329ebee...  
    39            16  https://i.scdn.co/image/ab67616d0000b273e5a25e...  
    40            18  https://i.scdn.co/image/ab67616d0000b2730e30fc...  
    41             6  https://i.scdn.co/image/ab67616d0000b2738e504c...  
    42             1  https://i.scdn.co/image/ab67616d0000b273e3a09a...  
    43             7  https://i.scdn.co/image/ab67616d0000b273e452a6...  
    44             1  https://i.scdn.co/image/ab67616d0000b273cd2ceb...  
    45            13  https://i.scdn.co/image/ab67616d0000b2739abdf1...  
    47            13  https://i.scdn.co/image/ab67616d0000b2739abdf1...  
    49            18  https://i.scdn.co/image/ab67616d0000b273a04841...  
    50            10  https://i.scdn.co/image/ab67616d0000b273926f43...  
    51             1  https://i.scdn.co/image/ab67616d0000b2734c8f09...  
    52            12  https://i.scdn.co/image/ab67616d0000b27382ce4c...  
    53            14  https://i.scdn.co/image/ab67616d0000b273cd2220...  
    54            15  https://i.scdn.co/image/ab67616d0000b2731917a0...  
    55            11  https://i.scdn.co/image/ab67616d0000b2738265a7...  
    56            13  https://i.scdn.co/image/ab67616d0000b273bb54dd...  
    57             2  https://i.scdn.co/image/ab67616d0000b2733d98a0...  
    58             2  https://i.scdn.co/image/ab67616d0000b273a04be3...  
    59            16  https://i.scdn.co/image/ab67616d0000b273dfddf1...  
    60            19  https://i.scdn.co/image/ab67616d0000b273881d8d...  
    61            22  https://i.scdn.co/image/ab67616d0000b273963265...  
    


```python
merged_df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 50 entries, 0 to 61
    Data columns (total 8 columns):
     #   Column            Non-Null Count  Dtype         
    ---  ------            --------------  -----         
     0   Track Name        50 non-null     object        
     1   Artist(s) Name    50 non-null     object        
     2   Album Name        50 non-null     object        
     3   Release Date      50 non-null     datetime64[ns]
     4   Popularity Score  50 non-null     int64         
     5   Track URL         50 non-null     object        
     6   Total Tracks      50 non-null     int64         
     7   Cover Art URL     50 non-null     object        
    dtypes: datetime64[ns](1), int64(2), object(5)
    memory usage: 3.5+ KB
    


```python
merged_df.to_csv(r"C:\Users\trapt\Downloads\Spotify Dataset.csv", index=False)
```


```python

```
