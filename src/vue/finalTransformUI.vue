<template>
<div>
  Final transform
  <b-switch v-model="canvasManager.canvas2d.useFinal"
            true-value="On"
            false-value="Off">{{ canvasManager.canvas2d.useFinal }}</b-switch>
  <div v-show="canvasManager.canvas2d.useFinal == 'On'">
    <b-field label="Affine"></b-field>
    <b-field label="a">
      <b-slider v-model="canvasManager.canvas2d.uFinalAffine[0]"
                v-on:dragging="sliderDragging"
                v-on:dragend="sliderDragEnd"
                :min="-10" :max="10" :step="0.01"></b-slider>
    </b-field>
      <b-field label="b">
        <b-slider v-model="canvasManager.canvas2d.uFinalAffine[1]"
                  v-on:dragging="sliderDragging"
                  v-on:dragend="sliderDragEnd"
                  :min="-10" :max="10" :step="0.01"></b-slider>
      </b-field>
      <b-field label="c">
        <b-slider v-model="canvasManager.canvas2d.uFinalAffine[2]"
                  v-on:dragging="sliderDragging"
                  v-on:dragend="sliderDragEnd"
                  :min="-10" :max="10" :step="0.01"></b-slider>
      </b-field>
      <b-field label="d">
        <b-slider v-model="canvasManager.canvas2d.uFinalAffine[3]"
                  v-on:dragging="sliderDragging"
                  v-on:dragend="sliderDragEnd"
                  :min="-10" :max="10" :step="0.01"></b-slider>
      </b-field>
      <b-field label="e">
        <b-slider v-model="canvasManager.canvas2d.uFinalAffine[4]"
                  v-on:dragging="sliderDragging"
                  v-on:dragend="sliderDragEnd"
                  :min="-10" :max="10" :step="0.01"></b-slider>
      </b-field>
      <b-field label="f">
        <b-slider v-model="canvasManager.canvas2d.uFinalAffine[5]"
                  v-on:dragging="sliderDragging"
                  v-on:dragend="sliderDragEnd"
                  :min="-10" :max="10" :step="0.01"></b-slider>
      </b-field>
      <b-field label="Variations">
        <b-select placeholder="Select a variation"
                  v-model="selectedVariation">
          <option
            v-for="option in variations"
            :value="option"
            :key="option.id">
            {{ option.name }}
          </option>
        </b-select>
      </b-field>
      <b-button @click="addVariation">Add</b-button>
      <div v-for="(finalVariation, index) in canvasManager.canvas2d.finalVariationList">
        <b-field :label="finalVariation.name"></b-field>
        <b-slider v-model="finalVariation.v"
                  v-on:dragging="sliderDragging"
                  v-on:dragend="sliderDragEnd"
                  :min="-10" :max="10" :step="0.01"></b-slider>
        <div v-for="param in finalVariation.params">
          <b-field :label="param.name"></b-field>
          <b-slider v-model="param.v"
                    v-on:dragging="sliderDragging"
                    v-on:dragend="sliderDragEnd"
                    :min="-10" :max="10" :step="0.01"></b-slider>
        </div>
        <b-button @click="deleteVariation(index)">Delete</b-button>
      </div>
      <b-field label="Post transform">
      </b-field>
      <b-field label="a">
        <b-slider v-model="canvasManager.canvas2d.uFinalPostAffine[0]"
                  v-on:dragging="sliderDragging"
                  v-on:dragend="sliderDragEnd"
                  :min="-10" :max="10" :step="0.01"></b-slider>
      </b-field>
      <b-field label="b">
        <b-slider v-model="canvasManager.canvas2d.uFinalPostAffine[1]"
                  v-on:dragging="sliderDragging"
                  v-on:dragend="sliderDragEnd"
                  :min="-10" :max="10" :step="0.01"></b-slider>
            </b-field>
      <b-field label="c">
        <b-slider v-model="canvasManager.canvas2d.uFinalPostAffine[2]"
                  v-on:dragging="sliderDragging"
                  v-on:dragend="sliderDragEnd"
                  :min="-10" :max="10" :step="0.01"></b-slider>
                  </b-field>
      <b-field label="d">
        <b-slider v-model="canvasManager.canvas2d.uFinalPostAffine[3]"
                  v-on:dragging="sliderDragging"
                  v-on:dragend="sliderDragEnd"
                  :min="-10" :max="10" :step="0.01"></b-slider>
                        </b-field>
      <b-field label="e">
        <b-slider v-model="canvasManager.canvas2d.uFinalPostAffine[4]"
                  v-on:dragging="sliderDragging"
                  v-on:dragend="sliderDragEnd"
                  :min="-10" :max="10" :step="0.01"></b-slider>
      </b-field>
      <b-field label="f">
        <b-slider v-model="canvasManager.canvas2d.uFinalPostAffine[5]"
                  v-on:dragging="sliderDragging"
                  v-on:dragend="sliderDragEnd"
                  :min="-10" :max="10" :step="0.01"></b-slider>
      </b-field>
  </div>
</div>
</template>

<script>
export default {
    props: ['canvasManager', 'variations'],
    data: function () {
        return {
            selectedVariation: undefined,
            finalVariationList: [],
        }
    },
    methods: {
        addVariation: function() {
            if (this.selectedVariation === undefined) return;
            this.canvasManager.canvas2d.finalVariationList.push({ ...this.selectedVariation});
            for(const v of this.canvasManager.canvas2d.finalVariationList) {
                for(let i = 0; i < v.params.length; i++){
                    v.params[i] = { ...v.params[i] };
                }
            }

            this.canvasManager.canvas2d.compileRenderShader();
            this.canvasManager.canvas2d.render();
        },
        deleteVariation: function(index) {
            this.canvasManager.canvas2d.finalVariationList.splice(index, 1);

            this.canvasManager.canvas2d.compileRenderShader();
            this.canvasManager.canvas2d.render();
        },
        sliderDragging: function(){
            this.canvasManager.canvas2d.isRendering = true;
        },
        sliderDragEnd: function() {
            this.canvasManager.canvas2d.isRendering = false;
        }
    }
}
</script>
