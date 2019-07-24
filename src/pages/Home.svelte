<script>
    import { onMount } from "svelte";

    import PostForm from "../components/PostForm.svelte";

    const apiBaseUrl = "https://ndb99xkpdk.execute-api.eu-west-2.amazonaws.com/dev"

    let posts = [];

    onMount(async () => {
        const res = await fetch(apiBaseUrl + '/posts')
        posts = await res.json()
    })

    function addPost({ detail: post }) {
        posts = [post, ...posts];
    }

    function editPost(post) {
        console.log(post)
    }

    // Uses promise chaining instead of async await for example purposes only
    function deletePost(id) {
        if (confirm('Are you sure?')) {
            fetch(`${apiBaseUrl}/post/${id}`, {
                method: 'DELETE'
            }).then(res => {
                return res.json();
            }).then(() => {
                posts = posts.filter(post => post.id !== id)
            })
        }
    }
</script>

<style>

.delete-btn {
    color: red !important;
}

.card .card-content .card-title {
    margin-bottom: 0; 
}

.card .card-content p.timestamp {
    color: #999;
    margin-bottom: 10px; 
}

</style>

<div class="row">
    <div class="col s6">
        <PostForm on:postCreated={addPost} />  
    </div>
</div>

<div class="row">
    {#if posts.length === 0}
        <h3>Loading posts ...</h3>
    {:else}
        {#each posts as post}
        <div class="col s6">
            <div class="card">
                <div class="card-content">
                    <p class="card-title">{post.title}</p>
                    <p>{post.createdAt}</p>
                    <p>{post.body}</p>                
                </div>
                <div>
                    <a href="#" on:click={() => editPost(post)}>Edit</a>
                    <a href="#" class="delete-btn" on:click={() => deletePost(post.id)}>Delete</a>
                </div>          
            </div> 
        </div>
        {/each}
    {/if}
</div>