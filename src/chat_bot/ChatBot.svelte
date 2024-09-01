<script>
  import axios from "axios";
  import avtollama from "./../../public/imgs/avtollama.png";
  import avtuser from "./../../public/imgs/avtuser.jpg";
  import { FontAwesomeIcon } from "@fortawesome/svelte-fontawesome";
  import { faArrowUp } from "@fortawesome/free-solid-svg-icons";

  let content = "";
  let question = "";
  let userQuestion = "";
  let scrollableContainer;

  let displayedText = ""; // Chuỗi sẽ hiển thị từ từ
  let index = 0;
  let typingSpeed = 50; // Tốc độ đánh chữ (ms/ký tự)
//   let fullText = ""

  const typeWriter = (/** @type {string} */ fullText) => {
    if (fullText && index < fullText.length) {
      displayedText += fullText.charAt(index);
      index++;
      setTimeout(()=>{
        typeWriter(fullText)
    }, typingSpeed);
    }
  };
  const handleSubmit = () => {
    let data = {
      question,
    };
    userQuestion = question;
    axios
      .post("http://127.0.0.1:8000/question", data)
      .then( (response) => {
        question = "";
        let fullText = response.data
    
       typeWriter(fullText);

        content += `
            <div class="userAsk">
                <p class="textContent">${userQuestion}</p>
                <img class="avt" src="${avtuser}"> 
            </div>
            <div class="AIReply">
                <img class="avt" src="${avtollama}"> 
                <p class="textContent" id="answer">${displayedText}</p>
            </div>`;


        setTimeout(() => {
          scrollableContainer.scrollTop = scrollableContainer.scrollHeight;
        }, 0);
      })
      .catch((error) => console.log(error));
  };
</script>

<h1>CHAT BOX</h1>
<div class="chatContent" bind:this={scrollableContainer}>
  {@html content}
</div>
<form on:submit|preventDefault={handleSubmit}>
  <div class="formInput">
    <input
      class="userInput"
      type="text"
      bind:value={question}
      placeholder="Type your question here"
    />
    <button class="submitButton" type="submit">
      <span>
        <FontAwesomeIcon class="sendIcon" icon={faArrowUp} />
      </span>
    </button>
  </div>
</form>
