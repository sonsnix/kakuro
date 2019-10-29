<template>
<v-container>
<v-container class="ma-0 mt-5 pa-0" style="width:600px; height:600px">
  <v-row v-for="(row, i) in tiles" :key="'row-' + i" justify="start" class="ma-0 pa-0">
    <v-col cols="1" v-for="(cell, j) in row" :key="'cell-' + j" class="ma-0 pa-0">
      <div style="height:50px; width:50px; border: solid 1px black; text-align:center" :class="tiles[i][j].type">
        <span v-show="tiles[i][j].type == 'filled'" style="line-height:50px; font-size:2em">
          {{tiles[i][j].value}}
        </span>
        <span v-show="tiles[i][j].type == 'sums'" style="line-height:50px; font-size:0.9em">
          <span v-if="tiles[i][j].vSum">{{tiles[i][j].vSum}}</span> \ <span v-if="tiles[i][j].hSum">{{tiles[i][j].hSum}}</span>
        </span>
        <span v-show="tiles[i][j].type == 'empty'" style="line-height:25px; font-size:0.7em; word-break:break-all">
          <span v-if="tiles[i][j].possibilities">{{tiles[i][j].possibilities.join(" ")}}</span>
        </span>

      </div>
    </v-col>
  </v-row>
</v-container>
<v-row>
  <v-col cols=1><v-btn @click.native="solve">Solve</v-btn></v-col>
  <v-col cols=1><v-btn @click.native="solveStep">Step</v-btn></v-col>
  <v-col cols=1><v-btn @click.native="clear">Clear</v-btn></v-col>
</v-row>
</v-container>
</template>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";

interface Tile {
  type: string;
  value?: number;
  hSum?: number;
  vSum?: number;
  possibilities?: number[];
}

@Component
export default class Main extends Vue {
  tiles: Tile[][] = [];

  created() {
    for(let i = 0; i < 10; i++) {
      this.tiles[i] = [];

      for(let j = 0; j < 10; j++) {
        this.tiles[i][j] = {type: "blank"};
      }
    }

    const positions = [
      [],
      [2,3,7,8],
      [1,2,3,5,6,7,8],
      [1,2,4,5,6,8,9],
      [2,3,4,8,9],
      [2,3,7,8],
      [1,2,6,7,8],
      [1,2,4,5,6,8,9],
      [2,3,4,5,7,8,9],
      [2,3,7,8]
    ];

    //   const positions = [
    //   [],
    //   [4,5,7,8],
    //   [2,3,4,5,6,7,8,9],
    //   [1,2,3,5,6,8,9],
    //   [1,2,4,5,7,8],
    //   [2,3,4,6,7,8],
    //   [2,3,5,6,8,9],
    //   [1,2,4,5,7,8,9],
    //   [1,2,3,4,5,6,7,8],
    //   [2,3,5,6]
    // ];

    for(const [row, cols] of positions.entries()) {
      for(const col of cols)
        this.tiles[row][col].type = "empty";
    }

    // const sums = [
    //   [[4, 15, 0], [5, 13, 0], [7, 7, 0], [8, 38, 0]],
    //   [[2, 40, 0], [3, 10, 9], [6, 4, 3], [9, 8, 0]],
    //   [[1, 5, 43]],
    //   [[0, 0, 15], [4, 3, 10], [7, 3, 3]],
    //   [[0, 0, 5], [3, 12, 3], [6, 9, 9]],
    //   [[1, 0, 16], [5, 16, 16], [9, 17, 0]],
    //   [[1, 7, 8], [4, 15, 6], [7, 9, 11]],
    //   [[0, 0, 8], [3, 14, 10], [6, 13, 16]],
    //   [[0, 0, 42]],
    //   [[1, 0, 13], [4, 0, 12]],
    // ];

        const sums = [
      [[2, 45, 0], [3, 3, 0], [7, 3, 0], [8, 45, 0]],
      [[1, 17, 8], [5, 16, 0], [6, 4, 3]],
      [[0, 0, 11], [4, 16, 17], [9, 17, 0]],
      [[0, 0, 17], [3, 3, 17], [7, 0, 16]],
      [[1, 0, 18], [7, 17, 13]],
      [[1, 17, 4], [6, 3, 11]],
      [[0, 0, 9], [4, 16, 0], [5, 3, 16], [9, 4, 0]],
      [[0, 0, 14], [3, 3, 10], [7, 16, 12]],
      [[1, 0, 19], [6, 0, 18]],
      [[1, 0, 5], [6, 0, 10]],
    ];
    
    for(const [row, cols] of sums.entries()) {
      for(const col of cols) {
        this.tiles[row][col[0]].type = "sums";
        this.tiles[row][col[0]].hSum = col[2];
        this.tiles[row][col[0]].vSum = col[1];
      }
    }

    
  }

  clear() {
     for(let row = 0; row < 10; row++) {
      for(let col = 0; col < 10; col++) {
        if(this.tiles[row][col].type == "empty" || this.tiles[row][col].type == "filled") {
          this.tiles[row][col] = {type: "empty"};
        }
      }
    }
    this.$forceUpdate();
  }

  solveStep() {
    console.log("Solving step");
    let changed = false;
    for(let row = 0; row < 10; row++) {
      for(let col = 0; col < 10; col++) {
        if(this.tiles[row][col].type == "empty") {
          this.$set(this.tiles[row][col], 'possibilities', this.checkTilePossibilities(row, col));
          if(this.tiles[row][col].possibilities!.length == 1) {
            this.$set(this.tiles[row][col], 'value', this.tiles[row][col].possibilities![0]);
            this.$set(this.tiles[row][col], 'type', "filled");
            changed = true;
          }
        }
      }
    }
    this.$forceUpdate();
    return changed;
  }

  solve() {
    let changed = true;
    while(changed) {
      changed = this.solveStep();
    }
    this.$forceUpdate();
  }

  checkTilePossibilities(tileRow: number, tileCol: number): number[] {

    // find hSum if not known
    if(!this.tiles[tileRow][tileCol].hSum) {
      for(let col = tileCol; col >= 0; col--) {
        if(this.tiles[tileRow][col].type == "sums") {
          this.tiles[tileRow][tileCol].hSum = this.tiles[tileRow][col].hSum!;
          break;
        }
      } 
    }

    // find vSum if not known
    if(!this.tiles[tileRow][tileCol].vSum) {
      for(let row = tileRow; row >= 0; row--) {
        if(this.tiles[row][tileCol].type == "sums") {
          this.tiles[tileRow][tileCol].vSum = this.tiles[row][tileCol].vSum!;
          break;
        }
      } 
    }

    // find hLength
    let hLength = 0;
    let hExisting: number[] = [];
    for(let col = tileCol; col < 10; col++) {
      if(this.tiles[tileRow][col].type == "blank" || this.tiles[tileRow][col].type == "sums")
        break;
      if(this.tiles[tileRow][col].type == "filled")
        hExisting.push(this.tiles[tileRow][col].value!);
      hLength++;
    }
    for(let col = tileCol-1; col >= 0; col--) {
      if(this.tiles[tileRow][col].type == "blank" || this.tiles[tileRow][col].type == "sums")
        break;
      if(this.tiles[tileRow][col].type == "filled")
        hExisting.push(this.tiles[tileRow][col].value!);
      hLength++;
    }

    // find vLength
    let vLength = 0;
    let vExisting: number[] = [];
    for(let row = tileRow; row < 10; row++) {
      if(this.tiles[row][tileCol].type == "blank" || this.tiles[row][tileCol].type == "sums")
        break;
      if(this.tiles[row][tileCol].type == "filled")
        vExisting.push(this.tiles[row][tileCol].value!);
      vLength++;
    }
    for(let row = tileRow-1; row >= 0; row--) {
      if(this.tiles[row][tileCol].type == "blank" || this.tiles[row][tileCol].type == "sums")
        break;
      if(this.tiles[row][tileCol].type == "filled")
        vExisting.push(this.tiles[row][tileCol].value!);
      vLength++;
    }

    const hPossibilities = Array.from(new Set(this.getSumCombinations(this.tiles[tileRow][tileCol].hSum!, hLength, hExisting).flat())).sort();
    const vPossibilities = Array.from(new Set(this.getSumCombinations(this.tiles[tileRow][tileCol].vSum!, vLength, vExisting).flat())).sort();

    return hPossibilities.filter(possibility => vPossibilities.includes(possibility));
  }

  getCombinations(length: number, options: number[] = [1, 2, 3, 4, 5, 6, 7, 8, 9]) {
    
    if(length == 1)
      return options.map(option => [option]);

    const combinations: number[][] = [];

    options.forEach((currentOption, optionIndex) => {
      const smallerCombos = this.getCombinations(
        length - 1,
        options.slice(optionIndex + 1)
      );

      // Concatenate currentOption with all combinations of smaller size.
      smallerCombos.forEach((smallerCombo) => {
        combinations.push([currentOption].concat(smallerCombo));
      });
    });

    return combinations;

  }

  getSumCombinations(sum: number, length: number, existing: number[]) {
    const reducedOptions = [1, 2, 3, 4, 5, 6, 7, 8, 9].filter(v => !existing.includes(v));

    const combinations = this.getCombinations(length-existing.length, reducedOptions);
    
    const existingSum = existing.reduce((tot: number, cur: number) => tot+cur, 0);

    return combinations.filter(comb => comb.reduce((tot: number, cur: number) => tot+cur) == sum-existingSum);
  }
}

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}

.blank {
  background-color: grey;
}

.sums {
  background-color:chocolate;
}
</style>
