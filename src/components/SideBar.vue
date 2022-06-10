<template>
  <div
    class="shape"
    v-for="(shape, index) in shapes"
    :key="index"
    :index="index"
    draggable
    @dragend="onDrop($event, shape.name)"
    @dragover.prevent
    @dragenter.prevent
  >
    <img
      :src=shape.image
      width="100"
      height="100"
    />
  </div>
  <input
    type="range"
    id="cowbell"
    name="cowbell"
    min="1"
    max="10"
    value=1
    @change="onChangeStageScale"
  >
</template>

<script lang="ts">
import { Options, Vue } from "vue-class-component";
import { Prop } from "vue-property-decorator";

@Options({
  components: {},
})
export default class HelloWorld extends Vue {
  @Prop() onDrop!: (evt: any, shape: string) => void;
  @Prop() onChangeStageScale!: (evt: any) => void;

  data() {
    return {
      shapes: [
        {
          name: "rect",
          image:
            "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcSvbPG1TL29-dIsQSnf-9kDoMRSOKBqBNAd-w&usqp=CAU",
        },
        {
          name: "circle",
          image:
            "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTCXxGQkNnwxfIfbAZun8ZyT9f2CLpgxAfPfg&usqp=CAU",
        },
        {
          name: "image",
          image: "https://konvajs.org/assets/darth-vader.jpg",
        },
        {
          name: "image",
          image: "https://konvajs.org/assets/yoda.jpg",
        },
      ],
    };
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.shape {
  border: 1px solid #ccc;
  height: 100px;
  width: 100px;
  align-items: center;
  justify-content: center;
  text-align: center;
  line-height: 100px;
  border-radius: 20px;
  overflow: hidden;
}
</style>
