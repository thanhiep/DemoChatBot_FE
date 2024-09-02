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
  let isLoading = false;
  let loader = "";

  const handleSubmit = async () => {
    isLoading = true;
    let data = {
      question,
    };
    let AIAnswer = "";
    userQuestion = question;
    question = "";
    loader = `
          <div class="userAsk">
                <p class="textContent">${userQuestion}</p>
                <img class="avt" src="${avtuser}"> 
            </div>
            <div class="AIReply">
                  <img class="avt" src="${avtollama}"> 
                  <div class="loader"></div>
            </div>
    `;
    setTimeout(() => {
      scrollableContainer.scrollTop = scrollableContainer.scrollHeight;
    }, 0);
    try {
      if (data.question != "") {
        const response = await axios.post(
          "http://127.0.0.1:8000/question",
          data
        );

        question = "";

        AIAnswer = response.data;

        content += `
            <div class="userAsk">
                <p class="textContent">${userQuestion}</p>
                <img class="avt" src="${avtuser}"> 
            </div>
            <div class="AIReply">
                <img class="avt" src="${avtollama}"> 
                <p class="textContent">${AIAnswer}</p>
            </div>`;
       
        setTimeout(() => {
          scrollableContainer.scrollTop = scrollableContainer.scrollHeight;
        }, 0);
      }
    } catch (error) {
      throw error;
    } finally {
      isLoading = false;
    }
  };
</script>

<h1>CHAT BOX</h1>
<div class="chatContent" bind:this={scrollableContainer}>
  {@html content}
  {#if isLoading}
    {@html loader}
  {/if}
</div>
<form on:submit|preventDefault={handleSubmit}>
  <div class="formInput">
    <input
      class="userInput"
      type="text"
      bind:value={question}
      placeholder="Type your question here"
    />
    <button disabled={isLoading} class="submitButton" type="submit">
      <span>
        <FontAwesomeIcon class="sendIcon" icon={faArrowUp} />
      </span>
    </button>
  </div>
</form>
