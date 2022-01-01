<script>
  import { question } from "../data-store.js";
  import { current } from "../data-store.js";
  import { createEventDispatcher } from "svelte";
  import { disable1, disable2 } from "../data-store.js";
  import Button from "./Button.svelte";
  import Nav from "./Nav.svelte";
  import { selectedanswer } from "../data-store.js";
  import App from "../App.svelte";
  import { rev } from "../data-store.js";
  import { startpage } from "../data-store.js";
  import { isconfirm } from "../data-store.js";

  let dispatch = createEventDispatcher();
  var explain = true;
  var currentselect = [];
  var home = false;
  let Heading;
  var exp;

  selectedanswer.subscribe((items) => {
    let t = items.filter((c, index) => {
      return items.indexOf(c) === index;
    });
    currentselect = [...t];
  });

  if ($current > 0) {
    disable2.set(false);
  }
  if ($current == 0) {
    disable1.set(false);
  }

  if ($current >= 10) {
    disable1.set(true);
  }

  function next() {
    $current += 1;

    disable1.set(false);
    disable2.set(false);

    if ($current == 10) {
      disable1.set(true);
    }
  }

  function prev() {
    $current -= 1;
    disable2.set(false);
    disable1.set(false);

    if ($current == 0) {
      disable1.set(false);
      disable2.set(true);
    }
  }

  function dash() {
    home = true;
    rev.set(false);
    startpage.set(true);
    isconfirm.set(false);
    location.reload();
  }
</script>

<link rel="stylesheet" href="style.css" />

<header>
  <Nav Heading={"uCertify Test Review"} />
</header>

<div class="review">
  <section class="section">
    {#each $question as dataItem, i (dataItem)}
      {#if i == $current}
        <div class="main-question">
          <div class="number">{i + 1}.</div>
          <div class="box">{JSON.parse(dataItem.content_text).question}</div>
        </div>
        <div class="question-section" class:top-shift={i == 2}>
          {#each JSON.parse(dataItem.content_text).answers as ans, index (ans)}
            <label
              for="ans{index}"
              id="option{index}"
              class="radio"
              class:bold={ans.is_correct == 1}
              class:not-bold={currentselect.includes(ans.answer) &&
                ans.is_correct == 0}
              >{String.fromCharCode(65 + index)}

              <input
                class="radio_input"
                type="radio"
                name="ans"
                id="ans{index}"
                is_correct={ans.is_correct}
                value={ans.answer}
                checked={ans.answer && ans.is_correct == 1 ? true : false}
                disabled
              />

              <div
                class:radio_radio={ans.is_correct == 1 || ans.answer}
                class:wrong={currentselect.includes(ans.answer) &&
                  ans.is_correct == 0}
              />
              {@html ans.answer}
            </label>
          {/each}

          <div class="explanation">
            {JSON.stringify(JSON.parse(dataItem.content_text).explanation)
              .replace('"', " ")
              .replace(/(?:\\[rn]|[\r\n]+)+/g, "")
              .replace("Answer", "")
              .replace(/Answer option.*/, "")
              .replace(/Answer.*/, "")
              .replace("option", "")
              .replace("is correct", "")
              .replace("<seq no=", "Option")
              .replace(/\\/g, "")
              .replace("/>", " is Correct")}
          </div>
        </div>
        <div class="bottom-nav">
          <Button
            style="button"
            margin="btn-bottom"
            type="button"
            id="prev"
            name="Prev-btn"
            caption="Previous"
            disabled={$disable2}
            on:click={prev}
          />
          <div class="numbering">
            <b>{i + 1} of 11</b>
          </div>
          <Button
            style="button"
            margin="btn-bottom"
            type="button"
            id="next"
            name="Next-btn"
            caption="Next"
            disabled={$disable1}
            on:click={next}
          />
          <Button
            style="button"
            margin="btn-bottom"
            type="button"
            id="dash"
            name="DashBoard-btn"
            caption="DashBoard"
            on:click={dash}
          />
        </div>
      {/if}
    {/each}
  </section>

  {#if home}
    <App />
  {/if}
</div>
