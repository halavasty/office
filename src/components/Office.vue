<template>
  <div>
    <v-toolbar
    dark
    color="teal"
  >
    <v-toolbar-title>Найти сотрудника</v-toolbar-title>
    <v-autocomplete
      v-model="select"
      :items="items"
      :search-input.sync="search"
      cache-items
      class="mx-4"
      flat
      hide-no-data
      hide-details
      label="введите cid"
      solo-inverted
    ></v-autocomplete>
    <v-btn icon>
      <v-icon>mdi-dots-vertical</v-icon>
    </v-btn>
  </v-toolbar>
  </div>
</template>

<script lang="ts">
import * as THREE from 'three';
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader';
import { Component, Vue, Watch } from 'vue-property-decorator';
import Scene from './Scene.vue';

@Component<Office>({
  mounted() {
    this.loadingOffice();
  },
  destroyed() {
    this.$parent.scene.remove(this.object3D);
  },
})
export default class Office extends Vue {

  private loader = new GLTFLoader();

  private objs: THREE.Mesh[] = [];

  private items: any = [];

  private search = null;

  private name: string|null = null;

  private states = [
    'arha',
    'kita',
    'dmim',
    'kanv',
    'kata',
    'kaan',
    'duan',
    'guva',
    'taol',
  ];

  private desks = [
    {
      "cid": 'kaan',
      'id': '1'
    },
    {
      "cid": 'taol',
      'id': '2'
    },
    {
      "cid": 'arha',
      'id': '3'
    },
    {
      "cid": 'dmim',
      'id': '4'
    },
    {
      "cid": 'kanv',
      'id': '5'
    },
    {
      "cid": 'kata',
      'id': '6'
    },
    {
      "cid": 'duan',
      'id': '7'
    },
    {
      "cid": 'guva',
      'id': '8'
    },
    {
      "cid": 'kita',
      'id': '9'
    },
  ];

  get select() {
    return this.name;
  }

   set select(value: string|null) {
    this.objs.map((item:any) => item.material.color.set(0xf361ed))
    this.name = value;
    const desk = this.desks.find(item => item.cid === value);
    if(desk){
      const obj: any = (this.objs.find(item => item.name.includes(desk.id)) || null);
       if(obj !== null){
        obj.material.color.set(0x0000ff)
       }
    }

  }

  object3D!: THREE.Group;

  $parent!: Scene;

  @Watch('search')
  onSearchChanged(val: string, oldVal: string){
    this.querySelections(val)
  }

  private querySelections (v: string) {
      this.items = this.states.filter(e => {
        return e.toLowerCase().includes(v)
      })
  };

  private loadingOffice() {
    this.$nextTick(() => {
      this.loader.load('/of2.glb', (gltf) => {
          this.$parent.scene.add(this.upgradeModel(gltf.scene));
      }, undefined, ( error ) => {
          console.error( error );
      });
    });
  }

  private upgradeModel(object3D: any) {
      object3D.children.map((item: any) => {
            if (item.name.includes('desk')){
                this.objs.push(item);

                item.cursor = 'pointer';
                item.material.color.set(0xf361ed);
                item.on('click', () => {
                  this.select = null;
                  item.material.color.set(0x0000ff);
                });
               item.on('mouseover',  () => {
                    item.material.color.set(0xff0000);
                });
                item.on('mouseout', () => {
                    if (!item.active) {
                        item.material.color.set(0xf361ed);
                    }
                });
            }
            if(item.name.includes('plane')){
                 item.material.color.set(0xb2f0e8)
            }

            return item;
        })

    return object3D
  }
}
</script>
