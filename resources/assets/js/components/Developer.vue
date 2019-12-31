<template>
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-md-12 mt-5">
                <passport-clients></passport-clients>
                <passport-authorized-clients></passport-authorized-clients>
                <passport-personal-access-tokens></passport-personal-access-tokens>
            </div>
        </div>
    </div>
</template>

<script>
import PassportClients from './passport/Clients.vue';
import PassportAuthorizedClients from './passport/AuthorizedClients.vue';
import PassportPersonalAccessTokens from './passport/PersonalAccessTokens.vue';

export default {
    components: {
        PassportClients,
        PassportAuthorizedClients,
        PassportPersonalAccessTokens
    },
    mounted() {
        console.log("Component mounted.");
    }
};
</script>
