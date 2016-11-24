# exoplayer-shoutcast
Shoutcast data source extension for Exoplayer with metadata support.

# Dependencies
* Exoplayer 2.0.4+
* OkHttp3 3.4.1+

#Installation (Gradle)
Add 'jitpack.io' as maven repository to build.gradle (project level)
```java
allprojects {
    repositories {
        jcenter()
        maven { url "https://jitpack.io" }
    }
}
```
Add github project path as dependency to build.gradle (module level)
```java
compile 'com.github.mattias-karlsson:exoplayer-shoutcast:master'
```
# Usage

```java
class PlaybackService extends Service implements ShoutcastMetadataListener {
    private final static String TAG = PlaybackService.class.getName();

    private SimpleExoPlayer player;
    private MediaSource audioSource;
    private final Call.Factory factory = new OkHttpClient.Builder().build();

    @Override
    public void onMetadataReceived(Metadata data) {
        Log.i(TAG, "Metadata Received");
        Log.i(TAG, "Artist: " + data.getArtist());
        Log.i(TAG, "Song: " + data.getSong());
        Log.i(TAG, "Show: " + data.getShow());
    }

    private void play(URL url) {
        Log.i(TAG, "Starting playback - " + url);

        Context context = getApplicationContext();

        // Produces DataSource instances through which media data is loaded.
        DataSource.Factory dataSourceFactory =  new ShoutcastDataSourceFactory(factory,
                Util.getUserAgent(context, context.getString(R.string.app_name)), null, PlaybackService.this);

        // This is the MediaSource representing the media to be played.
        audioSource = new ExtractorMediaSource(Uri.parse(url.toString()),
                dataSourceFactory, new DefaultExtractorsFactory(), null, null);

        // Prepare the player with the source.
        player = ExoPlayerFactory.newSimpleInstance(context, new DefaultTrackSelector(new Handler()), new DefaultLoadControl());
        player.prepare(audioSource);
        player.setPlayWhenReady(true);
    }
}
```
