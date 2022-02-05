<script>
    import { onMount } from 'svelte';
    import {API_URL} from '../../component/constant'
    import { fade } from 'svelte/transition';
    import Swal from "sweetalert2";

    let data = {
        category : "",
        subcategory: []
    }

        
    onMount(()=>{
        loadInitialData()
    })

    async function loadInitialData(){
            await fetch(`${API_URL}/products/category/list`,{
                    method: "POST"
                })
                .then(res=>res.json())
                .then(res => {

                    if (res.status == true) {
                        currentCategories = res.data
                      
                    }else {
                        currentCategories = null
                        Swal.fire({
                            icon: 'error',
                            title: 'Oops...',
                            text: 'Somthing went wong'
                        
                        })
                    }
                })
        }

    let image, text, category
    let currentCategories = null


        
    

    function addCategory (e){
        data.category = category
        
    }

    async function addSubCategory (e){
        if (!data.category) {
            Swal.fire({
                icon: 'error',
                title: 'Oops...',
                text: 'Please Add A Category First'
               
            })

            return false
        }else {
           
            data.subcategory = [...data.subcategory, {image, text}]
      
        }
        
    }

    function remove_subcategory (index) {

        console.log((data.subcategory).filter((item, pos)=>{
                pos != index? item: null
            }));
     
            data.subcategory = (data.subcategory).filter((item, pos)=>{
               if ( pos != index) {
                   return item
               }
            })
   
    }
        
    async function setCategory(){
        await fetch(`${API_URL}/products/category/create`,{
            method: "POST",
            headers: {
                'Content-Type' : 'application/json'
            },
            body: JSON.stringify(data)
        })
        .then(json => json.json())
        .then(res => {

            if (res.status == true) {

                image = null
                text = null
                category = null

                data = {
                    category : "",
                    subcategory: []
                }

                loadInitialData()

                Swal.fire(
                    'Good job!',
                    'You Category Added!',
                    'success'
                    )
            }else {
                throw new Error(res.message)
            }
        })
        .catch(res => {
            let reason = typeof res.message == 'string'? res.message: 'Somthing Went Wrong'
            Swal.fire({
                icon: 'error',
                title: 'Oops...',
                text: reason
               
            })
        })
    }
    async function deleteCategory(_id){
        await fetch(`${API_URL}/products/category/delete`,{
            method: "POST",
            headers: {
                'Content-Type' : 'application/json'
            },
            body: JSON.stringify({_id})
        })
        .then(json => json.json())
        .then(res => {

            if (res.status == true) {
             
                Swal.fire(
                    'Good job!',
                    'You Category deleted!',
                    'success'
                    )
                    loadInitialData()
            }else {
                throw new Error(res.message)
            }
        })
        .catch(res => {
            let reason = typeof res.message == 'string'? res.message: 'Somthing Went Wrong'
            Swal.fire({
                icon: 'error',
                title: 'Oops...',
                text: reason
               
            })
        })
    }


    function copyToClipBoard(value) {
       
        /* Copy the text inside the text field */
        navigator.clipboard.writeText(value);

        /* Alert the copied text */
        alert("Copied the text: " + value);
    }
        



</script>


<section transition:fade>

    <main>

        <div>
            <div>
                <h1 class="heading">Manage Category</h1>
            </div>
            <hr>
    
            <div class="form w-50 m-auto">
                <h1>Create Category</h1>
                <div class="create">
                    <form  on:submit|preventDefault={addCategory}>
                        <div class="d-flex">
                        <input required bind:value={category} class="form-input" type="text" placeholder="Enter Category">
                        <button class="btn-create">Add Category</button>
                        </div>
                    </form>
    
                    <form on:submit|preventDefault={addSubCategory}>
                        <div class="d-flex">
                            <div>
                                <input required bind:value={text} class="form-input" type="text" placeholder="Add Subcategory">
                                <input required bind:value={image} class="form-input" type="text" placeholder="Add Image">
                            </div>
                            <button class="btn-create">Add Sub Category</button>
                        </div>
                    </form>
                </div>
            </div>
    
            <div class="result">
              <div class="d-flex flex-column">
                  
                {#if data.category}
                    <h2>{data.category}</h2>
                {/if}
    
                {#if data.subcategory.length != 0}
                <ul>
                    {#each data.subcategory as list, i}
                    <li transition:fade> 
                        <img width="100px" src={list.image} alt=""> 
                        <p>{list.text}</p> 
                        <button 
                        on:click={()=>{remove_subcategory(i)}}
                        class="btn btn-danger">
                            delete
                        </button> 
                    </li>
                    {/each}
                        
                    </ul>
                    <button class="btn btn-success" on:click={setCategory}>Set Category</button>
                {/if}
                
              </div>
            </div>
    
            
    
        </div>
     
    </main>
    <hr>



<main>

    <div>
        <div>
            <h1 class="heading">List of Category</h1>
        </div>
        <hr>

        <div class="result">
          <div class="d-flex flex-column">
              


            {#if currentCategories != null}
            <ul>
                {#each currentCategories as list}
                <li transition:fade> 
                    <button 
                    on:click={()=>{deleteCategory(`${list._id}`)}}
                    class="btn btn-danger">
                        Delete
                    </button> 
                    <p>{list.category}</p> 
                    <button 
                    on:click={()=>{copyToClipBoard(`${list._id}`)}}
                    class="btn btn-warning">
                        Copy Id
                    </button> 
                </li>
                {/each}
                    
                </ul>
                
            {/if}
            
          </div>
        </div>

        

    </div>
 
</main>

</section>


<style lang="scss">

    .result {
        display: block;
        width: 50%;
        max-width: 600px;
        margin: auto;

       
        h2 {
            background-color: #0DB8DE;
            padding: 10px;
            border-radius: 10px;
            margin-top: 20px;
        }

        ul {
            padding: 0 50px;
            list-style: none;
            li {
                background-color: rgb(0, 113, 128);
                border-radius: 10px;
                padding: 10px;
                margin: 10px 0;
                display: flex;
                justify-content: start;
                align-items: center;
                
                p {
                    text-align: center;
                    width: 100%;
                    color: #fff;
                    font-weight: bold;
                }
            }
        }
    }
    .heading{
        color:#0b6e9c;
        text-align: center;
    }

    .form-input{
        width: 100%;
        background: #ffff;
        color: #012630;
        border: 1px solid #0DB8DE;
        font-size: 1em;
        font-weight: 600;
        padding: 20px;
        margin:5px;
    }
    // .form-select{
    //     width: 100%;
    //     border: 1px solid #0DB8DE;
    //     font-weight: 600;
    //     padding: 20px;
    //     overflow: hidden;
    //     margin:5px;
    //     text-transform: capitalize;
    // }

    .btn-create{
        margin: 10px;
        justify-content: end;
        color: #047088;
        font-weight: 500;
        padding: 8px 25px;
        border: 2px solid #0DB8DE;
        box-shadow: 0 5px 5px rgba(0, 0, 0, 0.2);
    }
    .btn-create:hover{
        color: #01181d;
        background: #0DB8DE;
        border: 2px solid #0DB8DE;
        box-shadow: 0 8px 8px rgba(0, 0, 0, 0.2);
    }
</style>