<template>
    <div class="particulars">
        <h4>Particulars</h4>
        <p :key="par"
            v-for="(particular, par) in particulars.split(/\r?\n/)"
        >{{ particular }}</p>
    </div>
</template>

<script>
export default {
    name: 'TransParticulars',

    props: {
        particulars: String
    }
};
</script>

<style lang="less" scoped>
.particulars {
    padding: 15px 0 15px 0;
    border: 1px solid rgba(38,50,56, .1);
    border-left: 0;
    border-right: 0;

    p {
        margin-bottom: 0;
    }
}
</style>
