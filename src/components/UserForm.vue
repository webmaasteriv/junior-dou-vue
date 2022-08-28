<template>
    <div>
        <form class="user_form_js">
            <div class="form_settings mb-3">
                <slot name="filterList"></slot>
            </div>
            <div class="user_output d-flex flex-column justify-content-around">
                <slot name="userList"></slot>
            </div>
            <div class="form_pagination mt-3">
                <nav aria-label="Page navigation example">
                    <slot name="pagination"></slot>
                </nav>
            </div>
        </form>
    </div>
</template>

<script setup>
</script>

<style scoped>

</style>