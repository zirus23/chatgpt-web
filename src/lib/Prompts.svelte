<script lang="ts">
  import DOMPurify from 'dompurify'
  import Typeahead from 'svelte-typeahead'
  import prompts from '../awesome-chatgpt-prompts/prompts.csv'

  const inputPrompt = (prompt: string) => {
    input.value = prompt
    input.style.height = 'auto'
    input.style.height = input.scrollHeight + 'px'
  }

  const extract = (prompt: typeof prompts[0]) => prompt.act

  export let input : HTMLTextAreaElement
</script>
