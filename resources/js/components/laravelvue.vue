<template>
    <div>
        <!-- <h1>Laravel + Vue VuePage</h1> -->
        <ProductComponent/>
    </div>
</template>

<script>
import ProductComponent from './ProductComponent.vue';
export default {
    name: 'laravelvue',
    components: [
        ProductComponent,
    ],
    components: { ProductComponent }
}
</script>
