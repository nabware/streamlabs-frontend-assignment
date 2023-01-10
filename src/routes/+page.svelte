<svelte:window on:click={onWindowClick}/>

<div class="sidenav">
    <button on:click={selectSourceType}>Add Source</button>
    {#each sources as source, i}
        <div style="margin-top: 5px">
            <img class="source-image" src={source.path} alt="source" />
            <button on:click={() => removeSource(i)}>Remove</button>
        </div>
    {/each}
</div>
  
<div class="main">
    <div class="video-container">
        <div id="screen-sharing" class="display"></div>
    </div>
    <div>
        <button>100%</button>
        <button>80%</button>
        <button>60%</button>
    </div>
    <div>
        <button>Bottom Right</button>
        <button>Bottom Left</button>
        <button>Right Side</button>
    </div>
    <div>
        <button>Chat</button>
        <button>Record</button>
        <button>Go Live</button>
    </div>
</div>

<!-- The Modal -->
<div id="myModal" class="modal">

    <!-- Modal content -->
    <div class="modal-content">
      <h2>Add a new media source</h2>
      <button id="screenshareButton" on:click={browseScreenshareSource} disabled={screenshare_enabled}>Screenshare</button>
      <button id="videoFeedButton" on:click={browseVideoFeedSource} disabled={video_feed_enabled}>Video Feed</button>
    </div>
  
</div>

<script lang="ts">
    import { onMount } from 'svelte';

    let sources: Array<Source> = [];

    interface Source {
        type: SourceType,
        path: string,
    }

    enum SourceType {
        Screenshare,
        VideoFeed
    }

    let screenshare_enabled: boolean = false;
    let video_feed_enabled: boolean = false;

    onMount(main);

    function main() {

    }

    function browseSource() {
        let input = document.createElement('input');
        input.type = 'file';
        input.onchange = _ => {
            // you can use this method to get file and perform respective operations
            if (!input.files) return;
            console.log(input.files[0]);
        };
        input.click();
    }

    function selectSourceType() {
        let modal = document.getElementById('myModal');
        if (!modal) return;
        modal.style.display = 'block';
    }

    function onWindowClick(event: Event) {
        let modal = document.getElementById('myModal');
        if (!modal) return;
        if (event.target === modal) {
            modal.style.display = 'none';
        }
    }

    function browseScreenshareSource() {
        let modal = document.getElementById('myModal');
        if (!modal) return;
        modal.style.display = 'none';
        let new_source: Source = { 
            type: SourceType.Screenshare,
            path: "./screenshare.webp"
        };
        sources = [...sources, new_source];
        screenshare_enabled = true;
    }

    function browseVideoFeedSource() {
        let modal = document.getElementById('myModal');
        if (!modal) return;
        modal.style.display = 'none';
        let new_source: Source = { 
            type:SourceType.VideoFeed,
            path: "./video_feed.webp",
        };
        sources = [...sources, new_source];
        video_feed_enabled = true;
    }

    function removeSource(sourceIndex: number) {
        let source: Source = sources[sourceIndex];
        sources.splice(sourceIndex, 1);
        sources = [...sources];
        switch (source.type) {
            case SourceType.Screenshare:
                screenshare_enabled = false;
                break;
            case SourceType.VideoFeed:
                video_feed_enabled = false;
                break;
        }
    }

</script>