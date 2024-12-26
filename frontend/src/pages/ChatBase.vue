<!-- <template>
  <div>
    <ChatBox />
  </div>
</template>

<script>
import ChatBox from '@/components/ChatBox.vue';

export default {
    setup () {
        return {}
    }
    
}
</script>

<style lang="scss" scoped>

</style> -->

<template>
  <div>
    <ChatBox />
  </div>
</template>
  
  <script>
  import ChatBox from '@/components/ChatBox.vue';
  
  export default {
    components: {
      ChatBox,
    },
  };
  </script>
  
  <style lang="scss" scoped>
  </style>
  