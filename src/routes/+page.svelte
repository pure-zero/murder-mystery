
<script lang="ts">
	import Person from "./person.svelte";
    interface IObjectKeys {
        [key: string]: string;
    }

    let chats: IObjectKeys = {Joe: 'Joe: Officer can I talk to you?\n', Cindy: 'Cindy: *Sobs*\n', Ksenia: 'Ksenia: *Ignores you*', Jason: 'Jason: *Quietly playing alone*'};
    let selected:string = ''
    $: selectedChat = chats[selected] || '';

    const setCurrentView = (name:string) => {
        selected = name;
        selectedChat = chats[selected];
    }

    const sendChat = async (event: { detail: { text: string, person: string; }; }) => {
        const person = event.detail.person;
        const text = event.detail.text;
        if(person && text) {
            const chatPromise = fetch('https://flask-service.oskm78i2rq00k.us-east-1.cs.amazonlightsail.com/chat', {
                method: 'POST',
                headers: {'Content-Type': 'application/json'},
                body: JSON.stringify({
                    name: person,
                    convo: chats[person],
                    prompt: 'You: ' + text
                })
            });
            chats[person] = chats[person] + '\nYou: ' + text;
            const chatResponse = await ((await chatPromise).json());
            chats[person] = chats[person] + "\n" + person + ":" + chatResponse.choices[0].text
        }
    }
</script>

<h1>Murder!</h1>

<button class="my-1 btn btn-primary" on:click={() => setCurrentView('Joe')}>Joe</button>
<button class="my-1 btn btn-primary" on:click={() => setCurrentView('Cindy')}>Cindy</button>
<button class="my-1 btn btn-primary" on:click={() => setCurrentView('Ksenia')}>Ksenia</button>
<button class="my-1 btn btn-primary" on:click={() => setCurrentView('Jason')}>Jason</button>

{#if selected}
    <Person name={selected} chat={selectedChat} on:message={sendChat}/>
{/if}

<style></style>