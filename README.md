# Music Streaming Platform

An object-oriented model of a music streaming platform (like Spotify), written in **Python**. It manages artists, albums, songs, podcasts and audiobooks, different user types and playlists, records listening sessions, and answers **10 analytics questions** about listening behaviour, such as *"Who are the top artists by listening time?"* or *"Which users have listened to a whole album?"*

Built as a university project for the Programming II course at IMC University of Applied Sciences Krems.

## Features

- **Domain model with 18 classes** using inheritance and composition
  - **Content:** `Track` → `Song` (`SingleRelease`, `AlbumTrack`), `Podcast` (`InterviewEpisode`, `NarrativeEpisode`), `AudiobookTrack`
  - **Users:** `User` → `FreeUser`, `PremiumUser`, `FamilyAccountUser` with `FamilyMember` sub-accounts
  - **Organisation:** `Artist`, `Album` (tracks kept in order), `Playlist`, `CollaborativePlaylist` (multiple contributors)
  - **Activity:** `ListeningSession` records who listened to what, when, and for how long
- **Central `StreamingPlatform` class** for registering and looking up tracks, users, artists, albums, playlists and sessions
- **10 analytics queries** (see below)
- **Automated tests** with pytest

## Class diagram

![Class diagram](StreamingPlatform.png)

The diagram source is in `ClassDiagram.puml` (PlantUML).

## Analytics queries

| # | Method | What it answers |
|---|---|---|
| Q1 | `total_listening_time_minutes(start, end)` | Total listening time across all users in a time window |
| Q2 | `avg_unique_tracks_per_premium_user(days=30)` | Average number of different tracks a premium user played recently |
| Q3 | `track_with_most_distinct_listeners()` | The track heard by the most different users |
| Q4 | `avg_session_duration_by_user_type()` | Average session length per user type, longest first |
| Q5 | `total_listening_time_underage_sub_users_minutes(age_threshold=18)` | Listening time of family members under a given age |
| Q6 | `top_artists_by_listening_time(n=5)` | Top artists by total listening time (songs only) |
| Q7 | `user_top_genre(user_id)` | A user's favourite genre and its share of their listening time |
| Q8 | `collaborative_playlists_with_many_artists(threshold=3)` | Collaborative playlists with songs from many different artists |
| Q9 | `avg_tracks_per_playlist_type()` | Average playlist size for standard vs. collaborative playlists |
| Q10 | `users_who_completed_albums()` | Users who listened to every track of an album, and which albums |

## Tech stack

- Python 3.12
- pytest
- PlantUML (class diagram)

## Project structure

```
src/streaming/
├── tracks.py       Track hierarchy (songs, podcasts, audiobooks)
├── users.py        User hierarchy (free, premium, family accounts)
├── artists.py      Artist
├── albums.py       Album
├── playlists.py    Playlist and CollaborativePlaylist
├── sessions.py     ListeningSession
└── platform.py     StreamingPlatform: registry and analytics queries
tests/
├── test_public.py  Tests for the 10 analytics queries
└── unit_tests/     Tests for the individual classes
```

## Installation

**Requirements:** Python 3.12 or newer.

```bash
git clone https://github.com/Chris-Markovic/REPO-NAME.git
cd REPO-NAME
python3 -m venv venv
source venv/bin/activate        # macOS / Linux
# venv\Scripts\activate         # Windows
pip install -e .
```

## Usage example

```python
from datetime import datetime
from streaming.platform import StreamingPlatform
from streaming.artists import Artist
from streaming.tracks import Song
from streaming.users import PremiumUser
from streaming.sessions import ListeningSession

platform = StreamingPlatform("MyStream")

artist = Artist("a1", "The Example Band", "rock")
song = Song("t1", "First Song", 210, "rock", artist)
artist.add_track(song)
user = PremiumUser("u1", "Anna", 25, subscription_start=datetime(2026, 1, 1))

platform.add_artist(artist)
platform.add_track(song)
platform.add_user(user)

session = ListeningSession("s1", user, song, datetime.now(), 180)  # listened for 180 seconds
platform.record_session(session)
user.add_session(session)

print(platform.top_artists_by_listening_time(n=1))  # [(<Artist>, 3.0)]
print(platform.user_top_genre("u1"))                # ('rock', 100.0)
```

## Running the tests

```bash
pytest                          # all tests
pytest tests/unit_tests/ -v     # only the class tests
pytest tests/test_public.py -v  # only the analytics queries
```
