<style>
    .content {
	  	max-width: 500px;
	  	margin: auto;
		text-align: center;
	}
	:global(.card) {
		border: 1px solid #000000;
	}
</style>

<script>
    import {login_state_wt, key_wt, board_name_wt, server_url} from "../stores";
    import {check_key} from "../checker";
    import Modal, { bind } from 'svelte-simple-modal';
    import { writable } from 'svelte/store';
    import Popup from "../Components/Popup.svelte";
    import {navigate} from "svelte-routing";
    import CollapsibleCard from '../Components/CollapsibleCard.svelte'
    import {onMount} from "svelte";



    let logged = false;
    let board_name = ""
    const current_url = window.location.href;

    let notes = []

    let local_key = current_url.split('/').reverse()[0]
    key_wt.set(local_key)
    check_key(local_key)

    login_state_wt.subscribe(value => {
		logged = value;
	});
    if (logged === false){
        navigate('/')
    }
    board_name_wt.subscribe(value => {
		board_name = value;
	});

    let url = '';

    server_url.subscribe(value => {
        url = value
    })


    const modal = writable(null);
    const showModal = () => modal.set(bind(Popup, { message: 'Enter your note' }));


    function load_notes(key){
        onMount(async () => {
        const response = await fetch(url + 'notes/'+key);
        const res = await response.json();
		notes = eval(res[0])
        notes = notes.reverse() // newest item on top
        })
    }

    load_notes(local_key)


</script>

<div class="content">
    {#if logged}
        <h1 id="board_name"><a href={"http://www.syncave.com/"+local_key} style="color: #aa8b56; text-decoration: none">
            <p>{board_name}</p>
        </a></h1>

        <Modal
          show={$modal}
          styleBg={{ backgroundColor: 'rgba(0,0,0,0.63)' }}
          styleWindow={{ boxShadow: '0 2px 5px 0 rgba(0, 0, 0, 0.15)' }}
          closeOnEsc=true
          closeOnOuterClick=true
        >
          <button id="add_note_button" on:click={showModal}>Add Note</button>
        </Modal>



            { #each notes as note } <!-- id, created, data -->
                <CollapsibleCard open={false} note_id={note.id} cave_key={local_key} note_text={note.data}>
                    <h3 slot='header'>{note.data}</h3>
                    <p slot='body' style="font-style: italic">created: {note.created}</p>
                </CollapsibleCard>
            { /each }

    {/if}

</div>