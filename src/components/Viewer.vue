<template>
  <div>

  </div>
</template>

<script>
import axios from "axios";

export default {
  name: 'Viewer',
  mounted() {

    const formData = new FormData();
    formData.append('instructions', JSON.stringify({
      parts: [
        {
          file: "document"
        }
      ]
    }))
    formData.append('document', fs.createReadStream('document.docx'));

    (async () => {
      try {
        const response = await axios.post('https://api.pspdfkit.com/build', formData, {
          headers: formData.getHeaders({
            'Authorization': 'Bearer pdf_live_vlahqVoN0WhEGukmHUgnNHstlAJhE2Fmw2WIY35Lx4B'
          }),
          responseType: "stream"
        })

        response.data.pipe(fs.createWriteStream("result.pdf"))
      } catch (e) {
        const errorString = await streamToString(e.response.data)
        console.log(errorString)
      }
    })()

    function streamToString(stream) {
      const chunks = []
      return new Promise((resolve, reject) => {
        stream.on("data", (chunk) => chunks.push(Buffer.from(chunk)))
        stream.on("error", (err) => reject(err))
        stream.on("end", () => resolve(Buffer.concat(chunks).toString("utf8")))
      })
    }

  },
  props: {
    msg: String
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}
</style>
