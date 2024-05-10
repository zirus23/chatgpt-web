<script lang="ts">
  import { apiKeyStorage, globalStorage, lastChatId, getChat, started, setGlobalSettingValueByKey, checkStateChange } from './Storage.svelte'
  import Footer from './Footer.svelte'
  import { replace } from 'svelte-spa-router'
  import { afterUpdate, onMount } from 'svelte'
  import { set as setOpenAI } from './providers/openai/util.svelte'
  import { hasActiveModels } from './Models.svelte'

$: apiKey = $apiKeyStorage

let pedalsEndpoint = $globalStorage.pedalsEndpoint
let hasModels = hasActiveModels()

onMount(() => {
    if (!$started) {
      $started = true
      // console.log('started', apiKey, $lastChatId, getChat($lastChatId))
      if (hasActiveModels() && getChat($lastChatId)) {
        const chatId = $lastChatId
        $lastChatId = 0
        replace(`/chat/${chatId}`)
      }
    }
    $lastChatId = 0
})

afterUpdate(() => {
    hasModels = hasActiveModels()
    pedalsEndpoint = $globalStorage.pedalsEndpoint
    $checkStateChange++
})


</script>

<section class="section">
  <article class="message">
    <div class="message-body">
    <p class="mb-4">
      <strong><a href="https://github.com/Niek/chatgpt-web" target="_blank">ChatGPT-web</a></strong>
      is a simple one-page web interface to the OpenAI ChatGPT API. To use it, you need to register for
      <a href="https://platform.openai.com/account/api-keys" target="_blank" rel="noreferrer">an OpenAI API key</a>
      first. OpenAI bills per token (usage-based), which means it is a lot cheaper than
      <a href="https://openai.com/blog/chatgpt-plus" target="_blank" rel="noreferrer">ChatGPT Plus</a>, unless you use
      more than 10 million tokens per month. All messages are stored in your browser's local storage, so everything is
      <strong>private</strong>. You can also close the browser tab and come back later to continue the conversation.
    </p>
    <p>
      As an alternative to OpenAI, you can also use Petals swarm as a free API option for open chat models like Llama 2. 
    </p>
    </div>
  </article>
  <article class="message" class:is-danger={!hasModels} class:is-warning={!apiKey} class:is-info={apiKey}>
    <div class="message-body">
      Set your OpenAI API key below:

      <form
        class="field has-addons has-addons-right"
        on:submit|preventDefault={(event) => {
          let val = ''
          if (event.target && event.target[0].value) {
            val = (event.target[0].value).trim()
          }
          setOpenAI({ apiKey: val })
          hasModels = hasActiveModels()
        }}
      >
        <p class="control is-expanded">
          <input
            aria-label="OpenAI API key"
            type="password"
            autocomplete="off"
            class="input"
            class:is-danger={!hasModels}
            class:is-warning={!apiKey} class:is-info={apiKey}
            value={apiKey}
          />
        </p>
        <p class="control">
          <button class="button is-info" type="submit">Save</button>
        </p>


      </form>

      {#if !apiKey}
        <p class:is-danger={!hasModels} class:is-warning={!apiKey}>
          Please enter your <a target="_blank" href="https://platform.openai.com/account/api-keys">OpenAI API key</a> above to use Open AI's ChatGPT API.
          At least one API must be enabled to use ChatGPT-web.
        </p>
      {/if}
    </div>
  </article>

  
  {#if apiKey}
    <article class="message is-info">
      <div class="message-body">
        Select an existing chat on the sidebar, or
        <a href={'#/chat/new'}>create a new chat</a>
      </div>
    </article>
  {/if}
</section>
<Footer pin={true} />