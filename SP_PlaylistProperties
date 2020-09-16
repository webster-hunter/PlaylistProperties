import spotipy as sp
import spotipy.util as util
import sys
from matplotlib import pyplot
from pprint import pprint
import time

# Info used to get token
username = input('Username: ')
scope = 'playlist-read-private'
CLIENT_ID = 'ac7d4932ffb44753803107f6d188ff4b'
CLIENT_SECRET = input('Client Secret: ')
REDIRECT_URI = 'http://localhost:8080'

# get and validate token
token = util.prompt_for_user_token(username, scope, CLIENT_ID, CLIENT_SECRET, REDIRECT_URI)

if token:
    sp = sp.Spotify(auth=token)
else:
    print("Can't get token for", username)
    sys.exit(0)

# get first 10 playlists and their ids
user_playlists = sp.current_user_playlists(limit=10)
playlist_ids = []
playlist_names = []
print('\nUSER PLAYLISTS:')
for i, item in enumerate(user_playlists['items']):
    print("%d. %s" % (i + 1, item['name']))
    playlist_ids.append(item['id'])
    playlist_names.append(item['name'])

# prompt user for playlist selection and validate
while True:
    playlist_selected = input('Playlist to be analyzed (1-10):')
    if 0 < int(playlist_selected) < 11:
        playlist_selected = int(playlist_selected) - 1
        break
    else:
        print('INVALID SELECTION\n')

# spotify audio property fields and descriptions
print('\nAUDIO PROPERTIES:')
fields = ['acousticness', 'danceability', 'duration', 'energy', 'instrumentalness', 'loudness', 'speechiness', 'tempo',
          'valence']
field_descriptions = ['A confidence measure from 0.0 to 1.0 of whether the track is acoustic. 1.0 represents high '
                      'confidence the track is acoustic.',
                      'The duration of the track in milliseconds.',
                      'Danceability describes how suitable a track is for dancing based on a combination of musical '
                      'elements including tempo, rhythm stability, beat strength, and overall regularity. A value of '
                      '0.0 is least danceable and 1.0 is most danceable.',
                      'Energy is a measure from 0.0 to 1.0 and represents a perceptual measure of intensity and '
                      'activity. Typically, energetic tracks feel fast, loud, and noisy. For example, death metal has '
                      'high energy, while a Bach prelude scores low on the scale. Perceptual features contributing to '
                      'this attribute include dynamic range, perceived loudness, timbre, onset rate, and general '
                      'entropy.',
                      'Predicts whether a track contains no vocals. “Ooh” and “aah” sounds are treated as '
                      'instrumental in this context. Rap or spoken word tracks are clearly “vocal”. The closer the '
                      'instrumentalness value is to 1.0, the greater likelihood the track contains no vocal content. '
                      'Values above 0.5 are intended to represent instrumental tracks, but confidence is higher as '
                      'the value approaches 1.0.',
                      'The overall loudness of a track in decibels (dB). Loudness values are averaged across the '
                      'entire track and are useful for comparing relative loudness of tracks. Loudness is the quality '
                      'of a sound that is the primary psychological correlate of physical strength (amplitude). '
                      'Values typical range between -60 and 0 db.',
                      'Speechiness detects the presence of spoken words in a track. The more exclusively speech-like '
                      'the recording (e.g. talk show, audio book, poetry), the closer to 1.0 the attribute value. '
                      'Values above 0.66 describe tracks that are probably made entirely of spoken words. Values '
                      'between 0.33 and 0.66 describe tracks that may contain both music and speech, '
                      'either in sections or layered, including such cases as rap music. Values below 0.33 most '
                      'likely represent music and other non-speech-like tracks.',
                      'The overall estimated tempo of a track in beats per minute (BPM). In musical terminology, '
                      'tempo is the speed or pace of a given piece and derives directly from the average beat '
                      'duration.',
                      'A measure from 0.0 to 1.0 describing the musical positiveness conveyed by a track. Tracks with '
                      'high valence sound more positive (e.g. happy, cheerful, euphoric), while tracks with low '
                      'valence sound more negative (e.g. sad, depressed, angry). '
                      ]

for i, field in enumerate(fields):
    print("%d. %s" % (i + 1, field))

# get the desired field
while True:
    field_selected = input('Select field to be measured (1-10, add a 1 before selection to get description):')
    # field is selected
    if 0 < int(field_selected) < 10:
        break
    # description is requested
    elif 10 < int(field_selected) < 20:
        print(fields[int(field_selected) - 11], ': ', field_descriptions[int(field_selected) - 11], '\n')
    # invalid selection
    else:
        pprint('INVALID SELECTION\n')

start_timer = time.time()

field_data = []
# get the id of the desired playlist
offset = 0
pid = 'spotify:playlist:' + playlist_ids[playlist_selected]

# since there is a max to the number of songs that can be pulled at a time,
# a loop needs to be used with an increasing offset to catch all tracks in a playlist
while True:
    result = sp.playlist_items(pid,
                               offset=offset,
                               fields='items.track.id,total',
                               additional_types=['track'])
    offset = offset + len(result['items'])

    # get data for desired field
    for n in range(len(result['items'])):
        tid = result['items'][n]['track']['id']  # track id
        features = sp.audio_features(tid)
        field_data.append(features[0][fields[int(field_selected) - 1]])  # append to field_data

    if len(result['items']) == 0:
        break


# set up plots
fig, axs = pyplot.subplots(1, 1, figsize=(10, 7), tight_layout=True)

# set up grid lines
axs.grid(b=True, color='grey',
         linestyle='-.', linewidth=0.5,
         alpha=0.6)

# axis labels
pyplot.xlabel(fields[int(field_selected) - 1])
pyplot.ylabel('count')

# graph title
title = playlist_names[int(playlist_selected)] + ': ' + fields[int(field_selected) - 1] + ' data'
pyplot.title(title)

axs.hist(field_data, bins=20)

end_timer = time.time()
print('Time Elapsed: ', (end_timer - start_timer))

pyplot.show()


