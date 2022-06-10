<template>
  <div class="container_wrap">
    <div class="nav_bar">
      <div class="nav_bar__form">
        <div class="shape_selector">
          <SideBar
            :onDrop="onDrop"
            :onChangeStageScale="handleChangeStageScale"
          />
        </div>
      </div>
    </div>
    <div id="container" />
  </div>
</template>

<script lang="ts">
import { Options, Vue } from "vue-class-component";
import SideBar from "./components/SideBar.vue";
import Konva from "konva";
@Options({
  components: {
    SideBar,
  },
})
export default class App extends Vue {
  private layer: any;
  private stage: any;
  private transformLayer: any;
  private transform: any;

  mounted() {
    let width = window.innerWidth * 0.78;
    let height = window.innerHeight * 0.95;

    this.stage = new Konva.Stage({
      container: "container",
      width: width,
      height: height,
      draggable: true,
    });

    this.transform = new Konva.Transformer();

    this.layer = new Konva.Layer();
    this.transformLayer = new Konva.Layer();

    this.transformLayer.add(this.transform);

    this.stage.add(this.layer);
    this.stage.add(this.transformLayer);

    this.stage.on("click tap", (e: any) => {
      for (let i = 0; i < this.transform.nodes().length; i++) {
        let node = this.transform.nodes()[i];
        node.draggable(false);
      }
      const nodeType = Object.getPrototypeOf(e.target).nodeType;
      if (nodeType === "Stage") {
        this.transform.nodes([]);
        return;
      }
      const metaPressed = e.evt.shiftKey || e.evt.ctrlKey || e.evt.metaKey;
      const isSelected = this.transform.nodes().indexOf(e.target) >= 0;

      if (!metaPressed && !isSelected) {
        this.transform.nodes([e.target]);
      } else if (metaPressed && isSelected) {
        const nodes = this.transform.nodes().slice();
        nodes.splice(nodes.indexOf(e.target), 1);
        this.transform.nodes(nodes);
      } else if (metaPressed && !isSelected) {
        const nodes = this.transform.nodes().concat([e.target]);
        this.transform.nodes(nodes);
      }
      for (let i = 0; i < this.transform.nodes().length; i++) {
        let node = this.transform.nodes()[i];
        node.draggable(true);
      }
    });

    let stageContainer = this.stage.container();
    stageContainer.tabIndex = 1;
    stageContainer.focus();

    stageContainer.addEventListener("keyup", (e: any) => {
      console.log(e.keyCode);
      switch (e.keyCode) {
        case 37: {
          for (let i = 0; i < this.transform.nodes().length; i++) {
            this.transform.nodes()[i].setX(this.transform.nodes()[i].x() - 50);
          }
          break;
        }
        case 38: {
          for (let i = 0; i < this.transform.nodes().length; i++) {
            this.transform.nodes()[i].setY(this.transform.nodes()[i].y() - 50);
          }
          break;
        }
        case 39: {
          for (let i = 0; i < this.transform.nodes().length; i++) {
            this.transform.nodes()[i].setX(this.transform.nodes()[i].x() + 50);
          }
          break;
        }
        case 40: {
          for (let i = 0; i < this.transform.nodes().length; i++) {
            this.transform.nodes()[i].setY(this.transform.nodes()[i].y() + 50);
          }
          break;
        }
        case 46: {
          for (let i = 0; i < this.transform.nodes().length; i++) {
            this.transform.nodes()[i].remove();
          }
          this.transform.nodes([]);
          break;
        }
      }
      e.preventDefault();
    });
  }

  handleChangeStageScale(e: any) {
    this.stage.scale({
      x: e.target.value,
      y: e.target.value,
    });
  }
  // create new shape
  onDrop(evt: any, shape: string) {
    let newShape: any;
    const { clientX, clientY } = evt;
    const { offsetLeft, offsetTop } = this.stage.container();

    let x = this.stage.attrs?.x || 0;
    let y = this.stage.attrs?.y || 0;

    let position = {
      x: clientX - offsetLeft - x,
      y: clientY - offsetTop - y,
    };

    switch (shape) {
      case "circle": {
        newShape = new Konva.Circle({
          x: position.x,
          y: position.y,
          width: 100,
          height: 100,
          fill: "orange",
          stroke: "red",
          strokeWidth: 4,
        });
        break;
      }
      case "rect": {
        newShape = new Konva.Rect({
          width: 100,
          height: 100,
          fill: "pink",
          stroke: "orange",
          strokeWidth: 4,
        });
        newShape.setX(position.x - newShape.width() / 2);
        newShape.setY(position.y - newShape.height() / 2);
        break;
      }
      case "image": {
        let itemURL = evt.target.src;
        Konva.Image.fromURL(itemURL, (image: any) => {
          this.layer.add(image);

          image.setY(position.y - image.height() / 2);
          image.setX(position.x - image.width() / 2);
          this.transform.nodes([image]);
          image.draggable(true);
        });

        return;
      }
      default: {
        newShape = new Konva.Rect({
          x: position.x,
          y: position.y,
          width: 100,
          height: 100,
          stroke: "orange",
          strokeWidth: 4,
        });
        newShape.setX(position.x - newShape.width() / 2);
        newShape.setY(position.y - newShape.height() / 2);
      }
    }

    for (let i = 0; i < this.transform.nodes().length; i++) {
      let node = this.transform.nodes()[i];
      node.draggable(false);
    }
    newShape.on("dragstart", function () {
      newShape.moveToTop();
    });

    this.transform.nodes([newShape]);
    newShape.draggable(true);
    this.layer.add(newShape);
  }
}
</script>

<style>
.nav_bar {
  background-color: rgb(203, 191, 191);
  width: 20%;
}
.nav_bar__form {
  padding: 20px 10px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
.shape_selector {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 10px;
}

#container {
  background-color: transparent;
  width: 80%;
}
.container_wrap {
  display: flex;
  height: 95vh;
  border: 1px solid #ccc;
}
</style>
