<template>
  <v-form ref="form">
    <v-row justify="center">
      <v-col cols="12">
        <v-text-field
          v-model="recipeName"
          label="Recipe name"
          :rules="validations.recipeRules"
        />
      </v-col>
      <v-btn color="accent" :loading="loading" @click="validate">
        save
      </v-btn>
    </v-row>
  </v-form>
</template>

<script>
import { mapMutations, mapActions, mapState } from 'vuex'

export default {
  data() {
    return {
      recipeName: '',
      validations: {
        recipeRules: [v => !!v || 'Recipe name is required']
      },
      loading: false
    }
  },
  computed: {
    ...mapState('recipe', ['ingredients', 'myRecipes'])
  },
  methods: {
    ...mapMutations('snackbar', ['setSnackbar']),
    ...mapActions('recipe', ['insertRecipe']),
    validate() {
      if (this.$refs.form.validate()) {
        this.createRecipe()
      }
    },
    async createRecipe() {
      if (this.myRecipes.length >= 10) {
        this.setSnackbar({
          show: true,
          color: 'error',
          text: 'You have reached the limit of 10 recipes'
        })
        return
      }

      try {
        this.loading = true

        const data = {
          recipe: this.ingredients,
          name: this.recipeName
        }

        await this.insertRecipe(data)

        this.loading = false
        this.setSnackbar({
          show: true,
          text: 'Recipe created!'
        })
        this.$router.replace({
          name: 'My Recipes'
        })
      } catch (error) {
        this.loading = false
        console.error('TCL: createFeedstock -> error', error)
        this.setSnackbar({
          show: true,
          color: 'error',
          text: 'there was an error creating the recipe'
        })
      }
    }
  }
}
</script>

<style lang="scss" scoped></style>
