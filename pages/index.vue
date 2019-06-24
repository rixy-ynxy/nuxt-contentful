<template>
  <vue-markdown>
    <!-- render data of the person -->
    <h1>{{ person.fields.name }}</h1>
    <!-- render blog posts -->
    <ul>
      <li v-for="post in posts" :key="post.id">
        {{ post.fields.title }}
      </li>
    </ul>
    <PostPreview :posts="posts"></PostPreview>
  </vue-markdown>
</template>

<script>
  import {createClient} from '~/plugins/contentful.js'
  import PostPreview from '~/components/PostPreview'

  const client = createClient()

  export default {
    // `env` is available in the context object
    asyncData ({env}) {
      return Promise.all([
        // fetch the owner of the blog
        client.getEntries({
          'sys.id': env.CTF_PERSON_ID
        }),
        // fetch all blog posts sorted by creation date
        client.getEntries({
          'content_type': env.CTF_BLOG_POST_TYPE_ID,
          order: '-sys.createdAt',
          'limit': 4
        })
      ]).then(([entries, posts]) => {
        // return data that should be available
        // in the template
        return {
          person: entries.items[0],
          posts: posts.items
        }
      }).catch(console.error)
    }
  }
</script>
