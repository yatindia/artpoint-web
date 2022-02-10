<script>
    import Add from "../../../component/category/add.svelte"
    import Edit from "../../../component/category/edit.svelte"


    let showCategory= true
    let editCategory = false


</script>



<div class="d-flex justify-content-center">
    <button on:click={()=>{
        showCategory = true
        editCategory = false
    }} class="btn btn-info m-1" >Add Category</button>
    <button on:click={()=>{
        showCategory = false
        editCategory = true
    }} class="btn btn-warning m-1" >Edit Category</button>
</div>



{#if showCategory}
    <Add/>
{/if}

{#if editCategory}
    <Edit/>
{/if}