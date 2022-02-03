<script>
  import { API_URL } from "../../../../component/constant";
  import { onMount } from "svelte";
  import Login from "../../../auth/login.svelte";

  export let id;

  let Order = [];
  let details = [];
  let process = [];

  onMount(() => {
    fetchOrder();
  });

  const fetchOrder = async () => {
    let bodyData = { order_id: id };
    const res = await fetch(`${API_URL}/order/single_order_view`, {
      method: "post",
      body: JSON.stringify(bodyData),
      headers: { "Content-Type": "application/json" },
    });
    const json = await res.json();
    const prod = await fetch(
      `${API_URL}/products/list/${json.data.product_id}`,
      {
        method: "post",
        headers: { "Content-Type": "application/json" },
      }
    );
    const prodJSON = await prod.json();
    const orderBy = await fetch(
      `${API_URL}/admin/distributer/read/${json.data.order_placed_by}`,
      {
        method: "post",
        headers: { "Content-Type": "application/json" },
      }
    );
    const orderJSON = await orderBy.json();
    if (json.data.order_processed_by) {
      const processBy = await fetch(
        `${API_URL}/admin/marketeer/read/${json.data.order_processed_by}`,
        {
          method: "post",
          headers: { "Content-Type": "application/json" },
        }
      );
      const processJSON = await processBy.json();
      process = processJSON.data;
    } else {
      process = [];
    }
    Order = {
      shipping_address: json.data.shipping_address,
      product_name: prodJSON.data.title,
      product_cat: prodJSON.data.category,
      product_subCat: prodJSON.data.subCategory,
      product_image: prodJSON.data.image,
      distributer_name: orderJSON.data.name,
      distributer_phone: orderJSON.data.phone,
      distributer_add: orderJSON.data.address,
      distributer_bPrice: orderJSON.data.basePrice,
      distributer_dPrice: orderJSON.data.deliveryPrice,
    };
    details = json.data.order_details;
  };

</script>

<main>
  <div class="container-fluid border p-2">
    <h4 class="text-secondary text-center p-2 m-2 border-bottom">
      Order Details
    </h4>
    <div class="container row justify-content-center p-4">
      <div class="col-md-6">
        <div class="table">
          <table class="border bg-light tableWid">
            <tbody>
              <tr>
                <th scope="row">Product Name:</th>
                <td>{Order.product_name}</td>
              </tr>
              <tr>
                <th scope="row">Category:</th>
                <td>{Order.product_cat}</td>
              </tr>
              <tr>
                <th scope="row">SubCategory:</th>
                <td>{Order.product_subCat}</td>
              </tr>
              {#if Order.product_image}
              <tr class="text-center">
                <img
                  src={`${Order.product_image}`}
                  alt="pic"
                  class="img img-responsive imagePic"
                />
              </tr>
              {/if}
            </tbody>
            {#if process}
            <tbody>
                <tr>
                  <th scope="row">Marketeer Name:</th>
                  <td>{process.name}</td>
                </tr>
                <tr>
                  <th scope="row">Phone:</th>
                  <td>{process.phone}</td>
                </tr>
                <tr>
                  <th scope="row">E-Mial:</th>
                  <td>{process.email}</td>
                </tr>
              </tbody>
            {/if}
          </table>
        </div>
      </div>
      <div class="col-md-6">
        <div class="table">
          <table class="border bg-light tableWid">
            <tbody>
                <tr>
                  <th scope="row">Ordered By:</th>
                  <td>{Order.distributer_name}</td>
                </tr>
                <tr>
                  <th scope="row">Distributer Phone:</th>
                  <td>{Order.distributer_phone}</td>
                </tr>
                <tr>
                  <th scope="row">Shipping Address:</th>
                  <td>{Order.shipping_address}</td>
                </tr>
                <tr>
                  <th scope="row">Order Details:</th>
                  <table>
                    <tr>
                      <td>Width: {details.width}</td>
                    </tr>
                    <tr>
                      <td>Height: {details.height}</td>
                    </tr>
                    <tr>
                      <td>Arc Top: {details.arcTop}</td>
                    </tr>
                    <tr>
                      <td>Arc Bottom: {details.arcBottom}</td>
                    </tr>
                    <tr>
                      <td>Varnish: {details.varnish}</td>
                    </tr>
                    <tr>
                      <td>White-Coat: {details.whiteCoat}</td>
                    </tr>
                    <tr>
                      <td>Sandwich: {details.sandwich}</td>
                    </tr>
                  </table>
                </tr>
                <tr>
                  <th scope="row">Message:</th>
                  <td>{details.message}</td>
                </tr>
                <tr>
                    <th scope="row">Distributer Address:</th>
                    <td>{Order.distributer_add}</td>
                  </tr>
                  <tr>
                    <td><b>Base Price: </b> {Order.distributer_bPrice}</td>
                    <td><b>Delivery Price: </b> {Order.distributer_dPrice}</td>
                  </tr>
              </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</main>

<style>
  .tableWid {
    width: 100%;
  }
  .imagePic {
    max-width: 300px;
    width: 250px;
    height: auto;
    margin: 10px;
    border: 2px dotted rgba(49, 49, 49, 0.37);
  }
</style>
