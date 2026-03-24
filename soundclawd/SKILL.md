---
name: soundclawd
description: Identify a track from a SoundCloud Live set screenshot and find its Apple Music and Spotify links via 1001tracklists.com. Use when a user shares a screenshot of a SoundCloud Live set (or mentions a SoundCloud live set, DJ mix, or radio show) and wants to know what track is playing at the current timestamp, or wants an Apple Music or Spotify link for that track.
---

# SoundClawd

Identify tracks from SoundCloud Live set screenshots by cross-referencing 1001tracklists.com, then return Apple Music and Spotify links.

## Workflow

### Step 1: Read the Screenshot

Analyze the provided screenshot to extract:

1. **Set name** — title of the live set (DJ name, event, mix title)
2. **Current timestamp** — playback position shown in the player (e.g. "1:23:45")

If either value is unclear, ask the user to clarify before proceeding.

### Step 2: Search 1001tracklists.com

Search for the tracklist using web search:

```
site:1001tracklists.com {set name}
```

Fallback queries if no results:
- `1001tracklists {DJ name} {event or mix name}`
- `1001tracklists {DJ name} {year}`

Fetch the matching tracklist page.

### Step 3: Match the Track at Timestamp

From the tracklist page:

- Find the track whose cue time contains or immediately precedes the screenshot timestamp
- Extract **track name** and **artist**
- If no cue times are listed, estimate by track position relative to total set length and inform the user the match is estimated

### Step 4: Get the Apple Music Link

1. Check the 1001tracklists page for an Apple Music link on the matched track
2. If not found, check the individual track page on 1001tracklists
3. Final fallback — search: `site:music.apple.com {artist} {track name}` or use the iTunes Search API:

```
https://itunes.apple.com/search?term={artist}+{track name}&entity=song&limit=5
```

Pick the best match from results and construct the Apple Music link from `trackViewUrl`.

### Step 5: Get the Spotify Link

1. Check the 1001tracklists page for a Spotify link on the matched track
2. If not found, check the individual track page on 1001tracklists
3. Final fallback — search: `site:open.spotify.com {artist} {track name}`

### Step 6: Output

```
Set:        {set name}
Timestamp:  {timestamp from screenshot}
Track:      {track name}
Artist:     {artist}
Apple Music: {apple_music_link}
Spotify:    {spotify_link}
```

If any step produces uncertain results, state what was found and what wasn't, and ask the user how to proceed.
