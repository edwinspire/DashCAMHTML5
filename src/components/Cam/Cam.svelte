<script>
    let videoContainer;
    let PartDataVideo = [];

    function Download(data) {

        let url = URL.createObjectURL(data);
        let link = document.createElement("a");
        link.download = "dash_cam.webm";
        link.href = url;
        link.style.display = "none";
        document.body.appendChild(link);
        link.click();
        link.remove();
        window.URL.revokeObjectURL(url);
    }

    function Record(stream) {
        videoContainer.srcObject = stream;
        var options = { mimeType: "video/webm; codecs=vp9" };
        var mediaRec = new MediaRecorder(stream, options);
        mediaRec.start();
        mediaRec.ondataavailable = (e) => {
            PartDataVideo.push(e.data);
        };

        mediaRec.onstop = () => {
            console.log("Finalizó la grabación");
            let DataBlob = new Blob(PartDataVideo, { type: "video/webm" });
            PartDataVideo = [];

            Download(DataBlob);
        };

        setTimeout(() => {
            mediaRec.stop()
        }, 10000);
    }
</script>

<div>
    <button
        on:click={() => {
            navigator.mediaDevices
                .getUserMedia({ audio: true, video: true })
                .then(Record)
                .catch((e) => {
                    console.log(e);
                });
        }}>Grabar</button>
    <video autoplay bind:this={videoContainer} />
</div>
