
<script>
  import {SvelteComponent} from "svelte";

  export let message = 'Hi';
  import {login_state_wt, key_wt, server_url} from "../stores";
  import { toast, SvelteToast } from '@zerodevx/svelte-toast'
  import {navigate} from "svelte-routing";

  let input_data = '';
  let local_key;

  let url = '';

  server_url.subscribe(value => {
        url = value
    })

// add note to cave
    async function add_note (cave_key, data) {
        const res = await fetch(url + 'addnote/', {
            method: 'POST',
            headers: {'Content-Type': 'application/json'},
            body: JSON.stringify({
                'cave_key':cave_key,
                'data':data
            })
        })
        const json = await res.json()
        let result = JSON.stringify(json)
        input_data = ''
        console.log(result)
        location.reload();
        /*
        toast.push("Note was added!", {
              theme: {
                '--toastBackground': '#ee7144',
                '--toastBarBackground': '#ce592c'
              }
            })
         */
    }

  key_wt.subscribe(value => {
		local_key = value;
	});
</script>

<p>{message}</p>

<form id="add_note_form" on:submit|preventDefault={() => add_note(local_key, input_data)}>
    <input type="text" bind:value={input_data} autofocus>
    <button disabled={!input_data}> Enter </button>
</form>

<SvelteToast></SvelteToast>