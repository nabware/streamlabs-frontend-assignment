<svelte:window on:click={onWindowClick} />

<div class="sidenav">
    <button on:click={selectSourceType}>Add Source</button>
    {#each screenshareSources as source, i}
        <div style="margin-top: 5px">
            <img class="source-image" src={source.path} alt="source" />
            <button on:click={() => {source.showOnStream = !source.showOnStream}}>{source.showOnStream ?" Hide on stream" : "Show on stream"}</button>
            <button on:click={() => removeSource(i, source.type)}>Remove</button>
        </div>
    {/each}
    {#each videoFeedSources as source, i}
        <div style="margin-top: 5px">
            <img class="source-image" src={source.path} alt="source" />
            <button on:click={() => {source.showOnStream = !source.showOnStream}}>{source.showOnStream ?" Hide on stream" : "Show on stream"}</button>
            <button on:click={() => removeSource(i, source.type)}>Remove</button>
        </div>
    {/each}
</div>
  
<div class="main">
    {#if screenshareSources.length == 1 && screenshareSources[0].showOnStream && (videoFeedSources.length == 0 || !videoFeedSources[0].showOnStream)}
        <div class="video-container" style="background-image: url('{screenshareSources[0].path}'); background-size: 100% 100%;"></div>
    {:else if videoFeedSources.length == 1 && videoFeedSources[0].showOnStream && (screenshareSources.length == 0 || !screenshareSources[0].showOnStream)}
        <div class="video-container" style="background-image: url('{videoFeedSources[0].path}'); background-size: {videoFeedSize};  background-repeat: no-repeat; background-position: center;"></div>
    {:else if screenshareSources.length == 1 && videoFeedSources.length == 1 && screenshareSources[0].showOnStream && videoFeedSources[0].showOnStream}
        {#if videoFeedPosition === "right"}
            <div class="video-container">
                <div style="width: 100%; height: 100%; background-image: url('{screenshareSources[0].path}'); background-size: 66% 80%; background-repeat: no-repeat; background-position: left;">
                    <div style="width: 100%; height: 100%; background-image: url('{videoFeedSources[0].path}'); background-size: 33% 80%; background-repeat: no-repeat; background-position: right;"></div>
                </div>
            </div>
        {:else}
            <div class="video-container" style="background-image: url('{screenshareSources[0].path}'); background-size: 100% 100%;">
                <div style="width: 100%; height: 100%; background-image: url('{videoFeedSources[0].path}'); background-size: 20% 20%; background-repeat: no-repeat; background-position: {videoFeedPosition};"></div>
            </div>
        {/if}
    {:else}
        <div class="video-container"></div>
    {/if}

    {#if videoFeedSources.length == 1 && videoFeedSources[0].showOnStream && (screenshareSources.length == 0 || !screenshareSources[0].showOnStream)}
        <div>
            <button on:click={() => setVideoFeedSize("100%")}>100%</button>
            <button on:click={() => setVideoFeedSize("80%")}>80%</button>
            <button on:click={() => setVideoFeedSize("60%")}>60%</button>
        </div>
    {:else if screenshareSources.length == 1 && videoFeedSources.length == 1 && screenshareSources[0].showOnStream && videoFeedSources[0].showOnStream}    
        <div>
            <button on:click={() => setVideoFeedPosition("bottom right")}>Bottom Right</button>
            <button on:click={() => setVideoFeedPosition("bottom left")}>Bottom Left</button>
            <button on:click={() => setVideoFeedPosition("right")}>Right Side</button>
        </div>
    {/if}

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
      <button id="screenshareButton" on:click={browseScreenshareSource} disabled={screenshareSources.length != 0 ? true : false}>Screenshare</button>
      <button id="videoFeedButton" on:click={browseVideoFeedSource} disabled={videoFeedSources.length != 0 ? true : false}>Video Feed</button>
    </div>
</div>

<script lang="ts">
    let screenshareSources: Array<Source> = [];
    let videoFeedSources: Array<Source> = [];
    let videoFeedSize: string = "100%";
    let videoFeedPosition: string = "bottom right";

    interface Source {
        type: SourceType,
        path: string,
        showOnStream: boolean,
    }

    enum SourceType {
        Screenshare,
        VideoFeed
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
        let newSource: Source = { 
            type: SourceType.Screenshare,
            path: "./screenshare.webp",
            showOnStream: false,
        };
        screenshareSources = [...screenshareSources, newSource];
    }

    function browseVideoFeedSource() {
        let modal = document.getElementById('myModal');
        if (!modal) return;
        modal.style.display = 'none';
        let newSource: Source = { 
            type: SourceType.VideoFeed,
            path: "./video_feed.webp",
            showOnStream: false,
        };
        videoFeedSources = [...videoFeedSources, newSource];
    }

    function removeSource(sourceIndex: number, sourceType: SourceType) {
        switch (sourceType) {
            case SourceType.Screenshare:
                screenshareSources.splice(sourceIndex, 1);
                screenshareSources = [...screenshareSources];
                break;
            case SourceType.VideoFeed:
                videoFeedSources.splice(sourceIndex, 1);
                videoFeedSources = [...videoFeedSources];
                break;
        }
    }

    function setVideoFeedSize(newSize: string) {
        videoFeedSize = newSize;
    }

    function setVideoFeedPosition(newPosition: string) {
        videoFeedPosition = newPosition;
    }

</script>