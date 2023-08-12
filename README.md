# oBundle BigCommerce Test

## Task 1
### "Create a product called Special Item... create a feature that will show the product's second image when it is hovered on."


#### I enhanced the card.html template in the product templates by incorporating a second image. This was achieved by restricting the usage to only two images from the product gallery and designating the image with an index of 1 as the second image.
```html
<div class="card-img-container">
    {{> components/common/responsive-img
    image=image
    id="defualt-card-image"
    class="card-image"
    fallback_size=theme_settings.productgallery_size
    lazyload=theme_settings.lazyload_mode
    default_image=theme_settings.default_image_product
    }}
    {{#each (limit images 2)}}
    {{#if @index '===' 1}}
    <img id="second-card-image" class="card-image"
        src="{{getImage this 'productgallery_size' (cdn theme_settings.default_image_product)}}"
        alt="{{this.alt}}" title="{{this.alt}}">
    {{/if}}
    {{/each}}
</div>
```

#### I updated the styles to ensure that the second image is displayed only when hovering over the first image.
```css
    #second-card-image {
        display: none;
    }

    .card-img-container:hover #second-card-image {
        display: block;
    }

    .card-img-container:hover #default-card-image {
        display: none;
    }
```

## Task 2
### "Add a button at the top of the category page... When clicked, the product will be added to the cart. Notify the user that the product has been added. If the cart has an item in it - show a button next to the Add All To Cart button which says Remove All Items. When clicked it should clear the cart and notify the user"

#### I introduced two new buttons, allowing users to easily add or remove all items. Utilizing an 'if' statement, I dynamically assess the presence of a cart ID (indicating items in the cart). Consequently, the 'Remove All' button is displayed when a cart ID is detected, and hidden when it's not.
```html
{{~inject 'cartId' cart_id}}
<div class="page-content" id="product-listing-container">
    {{#if customer}}
    <div class="customer-info-banner">
        <h1>{{customer.name}}</h1>
        <p>{{customer.email}}</p>
        <p>{{customer.phone}}</p>
    </div>
    {{/if}}
    <button onclick="addAllToCart()" class="button button--primary" type="button">
        Add All To Cart
    </button>

    {{#if cart_id}}
    <button onclick="removeAllFromCart()" class="button button--primary" type="button">
        Remove All Items
    </button>
    {{/if}}
    {{> components/category/product-listing}}
    {{{region name="category_below_content"}}}
</div>
```

#### Employing my store's API, I successfully implemented the addition of items to the cart through the utilization of existing line items on the page. Furthermore, I employed the cart ID to facilitate the seamless removal of these items.
```javascript 
let cartId = '{{cart_id}}';
const lineItemIds = document.querySelectorAll("[data-entity-id]");
const currentLineItemsArray = Array.from(lineItemIds).map(lineItemId => ({
    quantity: 1,
    productId: lineItemId.dataset.entityId
}))

const addAllToCart = async () => {
    await fetch(`https://crystal-fecteau.mybigcommerce.com/api/storefront/carts`, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: `{"lineItems":${JSON.stringify(currentLineItemsArray)}}`,
    }).then((res) => {
        alert('Items added to cart');
        location.reload();
    }).catch(error => console.error(error));
}


const removeAllFromCart = async () => {
    if (cartId) {
        await fetch(`https://crystal-fecteau.mybigcommerce.com/api/storefront/carts/${cartId}`, {
            method: "DELETE",
            credentials: "same-origin",
            headers: { "Content-Type": "application/json" },
        }).then(res => {
            alert('Items removed from cart');
            location.reload();
        }).catch(error => console.error(error));
    }
}
```

## Task 3 (bonus)
### If a customer is logged in - at the top of the category page show a banner that shows some customer details...

#### By utilizing an 'if' statement, I dynamically determine whether a customer is logged in. If a customer is indeed logged in, their information is displayed.
```html        
{{#if customer}}
        <div class="customer-info-banner">
            <h1>{{customer.name}}</h1>
            <p>{{customer.email}}</p>
            <p>{{customer.phone}}</p>
        </div>
{{/if}}
```
