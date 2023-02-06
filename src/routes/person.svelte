<script lang="ts">
    import { createEventDispatcher } from 'svelte';
    export let name:string; 
    export let chat: string;
    const dispatch = createEventDispatcher();
    interface Message {
        name: string
        message: string
    }
    let message = '';
    let messages:Message[] = [];
    $: {
        chatChanged(chat)
    }

    const chatChanged = (chat:string) => {
        messages = [];
        if(chat) {
            chat.split('\n').forEach((message) => {
                if(message) {
                    const parts = message.split(':');
                    if(parts.length === 2) {
                        messages.push({name: parts[0], message: parts[1]});
                    }
                }
            })
        }
    }

    const submit = () => {
        dispatch('message', {
			text: message,
            person: name
		});
        message = '';
    }
</script>

<div class="px-4 pt-5 my-5 text-center border-bottom">
    <h2 class="display-4 fw-bold">{name}</h2>
</div>

<div class="container">
    <div class="d-flex flex-column align-items-stretch flex-shrink-0 bg-white">
        <div class="list-group list-group-flush border-bottom scrollarea">
            {#each messages as msg}
                <div class="list-group-item list-group-item-action py-3 lh-tight">
                    <div class="d-flex w-100 align-items-center justify-content-between">
                        <strong class="mb-1">{msg.name}</strong>
                    </div>
                    <div class="col-10 mb-1 small">{msg.message}</div>
                </div>
            {/each}
        </div>
    </div>
    <form on:submit|preventDefault={submit}>
        <input class="form-control" placeholder="Write a message" bind:value={message}/>
    </form>
</div>