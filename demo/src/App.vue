<template>
  <div class="demo_container">
    <div class="buttons_box">
      <button v-on:click="check_cell">Check Cell [3,1]</button>
      <button v-on:click="change_snake_class">Set class of 'snake'</button>
      <button v-on:click="delete_row">Delete 'bird'</button>
      <button v-on:click="add_row">Add 'goldfish'</button>
    </div>
    <table-comp
        title="Pets (Add/Delete/Change cell style)"
        :rows="rows"
        :headings="headings"
        :table_height="table_height"
        :column_widths="column_widths"
        :css_variables="css_variables"
        v-on:table_comp_row="show_row"
        v-on:table_comp_cell="show_cell">
    </table-comp>
  </div>
</template>

<script>
  import TableComp from 'tablecomp';

  export default {
    name: 'App',
    data: function(){
      return {
        headings: ['My Pets', 'Favorite Food', "Pet's Name", "Pet's Age"],
        rows: [
          [['dog',''], ['biscuits',''], ['Fido',''], [7.09,'']],
          [['cat',''], ['fish',''], ['Pussy',''], [3.345,'']],
          [['snake',''], ['rats',''], ['Sneaky',''], [10,'']],
          [['hamster',''], ['popcorn',''], ['Tumbler',''], [5.60,'']],
          [['bird',''], ['seeds',''], ['Sweety',''], [12,'']],
          [['turtle',''], ['lettuce',''], ['Speedy',''], [20.33,'']],
          [['pig',''], ['potatoes',''], ['Porky',''], [13.5,'']],
          [['cow',''], ['grass',''], ['Molly',''], [14,'']]
        ],
        table_height: 80,
        column_widths: [100,160,140,110],
        css_variables: {
          table_comp_title_color: 'white',
          table_comp_thead_color: 'white',
          table_comp_thead_background: 'black',
          table_comp_thead_border_bottom: '2px solid white',
          table_comp_row_color: 'gray',
          table_comp_row_selected_color: 'gold',
          table_comp_row_border_bottom: '1px solid white',
          table_comp_row_odd_background: 'black',
          table_comp_row_even_background: 'black'
        }
      };
    },
    components: {
      TableComp
    },
    methods: {
      add_row: function(){
        this.rows.push([['goldfish',''],['fish food',''],['Goldie',''],[4.3,'']]);
      },
      delete_row: function(){
        this.rows.splice(4,1);
      },
      //parent to child - modify a cell class
      check_cell: function(){
        this.rows[3].splice(1,1,[this.rows[3][1][0],'cell_check']);
      },
      change_snake_class: function(){
        this.rows[2].splice(0,1,['snake','snake_cell']);
      },
      //child to parent -- respond to row event
      show_row: function(obj){
        console.log(`Row clicked: ${obj.row_values}`);
      },
      //child to parent -- respond to cell event
      show_cell: function(obj){
        console.log(`Cell clicked:
        Row Index: ${obj.row_index}
        Cell Column: ${obj.col_index}
        Cell Value: ${obj.cell_value}
        Cell Class: ${obj.cell_class}`);
      }
    }
  }
</script>

<style>
  .demo_container {
    height: 40rem;
    width: 60rem;
    background-color: #000000;
    display: flex;
    flex-direction: column;
    padding: 2rem;
  }
  .buttons_box {
    display: flex;
    flex-direction: row;
    justify-content: space-around;
    margin-bottom: 2rem;
  }
  .cell_check::before {
    content: '\2713';
    display: inline-block;
    vertical-align: .2em;
    width: .8em;
    height: .8em;
    margin-right: .2em;
    background: transparent;
    text-indent: .15em;
    line-height: .65;
    color: white;
  }
  .snake_cell {
    color: red;
    font-weight:bold;
    font-style:italic;
    text-decoration:line-through;
  }
</style>