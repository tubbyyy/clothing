<script>
    import axios from "axios"
    import cors from "cors"
    
    let inputValue = ""
    let templateId = ""
    let labelValue = "Nothing yet..."
    let imageUrl = ""
    let location1 = ""
    let location2 = ""

    function handleKeyPress(event) {
        if (event.key === 'Enter') {
            labelValue = inputValue || "Nothing yet..."
            downloadImage(inputValue)
        }
    }

    function extractId(text) {
        const idRegex = /\?id=([^\s&]+)(?=<\/url>)/
        const match = text.match(idRegex)
        return match[1]
    }

    async function downloadImage(assetId) {
        try {
            const response1 = await axios.get(`https://assetdelivery.roproxy.com/v2/assetId/${assetId}`);
            location1 = response1.data.locations[0].location
            const response2 = await axios.get(location1)
            templateId = extractId(response2.data)
            const response3 = await axios.get(`https://assetdelivery.roproxy.com/v2/assetId/${templateId}`);
            imageUrl = response3.data.locations[0].location
        } catch (error) {
            console.error('Error downloading image:', error);
        }
    }
</script>

<main>
    <h1>Get Clothing Asset</h1>

    <label for="inputField">Clothing ID: <span>{labelValue}</span></label>
    
    <input 
        type="text" 
        id="inputField" 
        placeholder="Enter Clothing ID..." 
        bind:value={inputValue}
        on:keypress={handleKeyPress}
    />

    {#if imageUrl}
        <div>
            <h2>Template:</h2>
            <img src={imageUrl} alt="Thumbnail"/>
        </div>
    {/if}
</main>

<style>
    :global(body) {
        background-color: #fefcac;
    }
    main {
        font-family: Inter, sans-serif;
        display: flex;
        justify-content: center;
        align-items: center;
        flex-direction: column;
        margin: 50px;
    }
    label {
        font-size: 20px;
        display: block;
        margin-bottom: 10px;
    }
    input {
        font-size: 18px;
        padding: 8px;
        margin-bottom: 20px;
    }
</style>
