<template>
  <div class="app"></div>
</template>

<script lang="ts">
/* eslint-disable */
import { defineComponent } from "vue";

// matterJS
import * as Matter from "matter-js";

export default defineComponent({
  name: "App",
  data() {
    return {
      Engine: Matter.Engine,
      engine: null,
      Render: Matter.Render,
      render: null,
      World: Matter.World,
      Bodies: Matter.Bodies,
      Constraint: Matter.Constraint,
      elements: [] as any[],
      constraintOptions: {},
      body: {
        parameters: [400, 600, 200, 80] as any[],
      },
      limbs: [
        {
          parameters: [300, 100, 40, 200] as any[],
        },
        {
          parameters: [500, 300, 40, 200] as any[],
        },
      ],
      ground: {
        parameters: [400, 610, 810, 60, { isStatic: true }] as any[],
      },
    };
  },
  mounted() {
    var limbCategory = 0x0001,
      bodyCategory = 0x0002;

    this.engine = this.Engine.create();
    this.render = this.Render.create({
      element: document.body,
      engine: this.engine,
    });

    // BODY
    this.body.parameters.push({
      collisionFilter: { mask: bodyCategory },
    });
    this.elements.push(this.createBody(this.body.parameters));

    // LIMBS
    this.limbs.map((limb: any) => {
      limb.parameters.push({
        collisionFilter: { mask: limbCategory },
      });
      this.elements.push(this.createRectangle(limb.parameters));

      // Create constraint
      this.constraintOptions = {
        bodyA: this.elements[0],
        bodyB: this.elements[this.elements.length - 1],
        stiffness: 0.99,
        length: 20,
      };
      this.elements.push(this.createConstaraint(this.constraintOptions));
    });

    this.ground.parameters.push({
      collisionFilter: { mask: limbCategory | bodyCategory },
    });
    this.elements.push(this.createGround(this.ground.parameters));
    this.populateWorld(this.engine, this.elements);
    this.elements.forEach((element) => {
      console.log(element.collisionFilter?.mask);
    });
    this.Engine.run(this.engine);
    this.Render.run(this.render);
  },
  methods: {
    createRectangle(args: number[]) {
      return this.Bodies.rectangle(...args);
    },
    createGround(args: any[]) {
      return this.Bodies.rectangle(...args);
    },
    createBody(args: any[]) {
      return this.Bodies.rectangle(...args);
    },
    createConstaraint(options: any) {
      return this.Constraint.create(options);
    },
    populateWorld(engine: any, elements: any[]) {
      this.World.add(engine.world, elements);
    },
  },
  computed: {},
});
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}
</style>
