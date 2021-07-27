<script lang="ts">
    import UserDetails from "./UserDetail.svelte";

    type User = {
        login: string;
        avatar_url: string;
        showDetails: boolean;
    };

    async function getUsers(): Promise<User[]> {
        const res = await fetch('https:/api.github.com/users');
        const users = await res.json();

        if (res.ok) {
            return users;
        } else {
            throw new Error(users);
        }
    }

    $: allUsersPromise = getUsers();
</script>

<link href="https://unpkg.com/tailwindcss@^2/dist/tailwind.min.css" rel="stylesheet">

<section class="w-1/2 m-auto border border-gray-400 p-8 rounded">
    {#await allUsersPromise then users}
        {#each users as user}
            <div class="m-4 cursor-pointer" on:click={() => (user.showDetails = true)}>
                <div class="flex">
                    <img class="rounded-full w-12" src={user.avatar_url} alt="avatar" />
                    <p class="my-auto font-semibold ml-2">{user.login}</p>
                </div>
                {#if user.showDetails}
                    <UserDetails
                        userLogin={user.login}
                        on:closeDetails={() => {setTimeout(() => (user.showDetails = false));}}
                    />
                {/if}
            </div>
        {/each}
    {/await}
</section>