<template>
  <div>
    <div class="tableComp" v-if="rows !== null" :style="table_style">
      <div class="tableComp_title">{{title}}</div>
      <table class="tableComp_table">
        <thead class="tableComp_thead" v-if="headings !== null">
        <tr>
          <th
              v-for="(head,col_index) in headings"
              :key="col_index"
              :style="head_styles[col_index]"
              v-on:click="sort_column(col_index)">
            {{head}}
          </th>
        </tr>
        </thead>

        <tbody class="tableComp_tbody" :style="tbody_style">
        <tr
            class="tableComp_tr__notselected"
            v-for="(row,row_index) in rows"
            :key="row_index"
            v-on:click="row_clicked($event,row_index)">
          <td
              v-for="(cell,col_index) in row"
              :key="col_index"
              :style="td_styles[row_index][col_index]"
              :class="cell[1]"
              v-on:click="cell_clicked(row_index,col_index)">
            {{cell[0]}}
          </td>
        </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'TableComp',
    data(){
      return {
        head_styles: null,
        td_styles: null,
        selected_tr: null,
        sort_col_idx: 0,
        sort_descending: false
      }
    },
    props: {
      rows: {
        type: Array,
        default: null
      },
      title: {
        type: String,
        default: null
      },
      table_height: {
        type: Number,
        default: 300
      },
      headings: {
        type: Array,
        default: null
      },
      column_widths: {
        type: Array,
        default: null
      },
      css_variables: {
        type: Object,
        default: null
      }
    },
    computed: {
      table_style: function(){
        let row_styles= [];
        let table_width_style = '';
        const row_length = this.rows[0].length;
        if(this.column_widths === null){
          table_width_style = `width:${(120 * row_length)}px;`;
          for(let i=0; i<row_length; i++){
            row_styles.push('width:120px');
          }
        }else{
          let total = 0;
          for(let i=0; i<row_length; i++){
            if(this.column_widths[i] === null){
              total += 120;
              row_styles.push('width:120px');
            }else{
              total += this.column_widths[i];
              row_styles.push(`width:${this.column_widths[i]}px`);
            }
          }
          table_width_style = `width:${total + 8}px;`;
        }
        this.head_styles = row_styles;
        this.td_styles = [];
        for(let i =0; i < this.rows.length; i++){
          this.td_styles.push(row_styles);
        }
        return table_width_style;
      },
      tbody_style: function(){
        return `height: ${this.table_height}px;`;
      }
    },
    methods: {
      sort_column: function(col_index){
        if(col_index === this.sort_col_idx){
          this.sort_descending = !this.sort_descending;
        }else{
          this.sort_col_idx = col_index;
        }
        const self = this;
        this.rows.sort(sort_function);
        function sort_function(a, b){
          let x = a[col_index][0];
          let y = b[col_index][0];
          if(typeof x === 'string'){
            x = x.toLowerCase();
            y = y.toLowerCase();
          }
          if(self.sort_descending){
            return ((x < y) ? 1 : ((x > y) ? -1 : 0));
          }else{
            return ((x < y) ? -1 : ((x > y) ? 1 : 0));
          }
        }
      },
      //child to parent -- send selected row
      row_clicked: function(e,row_index){
        const row_values = [];
        const row_classes = [];
        for(const [idx,cell] of this.rows[row_index].entries()){
          row_values.push(cell[0]);
          row_classes.push(cell[1]);
        }
        if(this.selected_tr){
          this.selected_tr.classList.remove('tableComp_tr__selected');
          this.selected_tr.classList.add('tableComp_tr__notselected');
        }
        e.currentTarget.classList.remove('tableComp_tr__notselected');
        e.currentTarget.classList.add('tableComp_tr__selected');
        this.selected_tr = e.currentTarget;

        this.$emit('table_comp_row',
          {
            row_index: row_index,
            row_values: row_values,
            row_classes: row_classes
          }
        );
      },
      //child to parent -- send selected cell
      cell_clicked: function(row_index, col_index){
        const info = {
          row_index: row_index,
          col_index: col_index,
          cell_value: this.rows[row_index][col_index][0],
          cell_class: this.rows[row_index][col_index][1]
        };
        this.$emit('table_comp_cell',info);
      }
    },
    mounted(){
      if(this.css_variables !== null){
        for(let key of Object.keys(this.css_variables)){
          this.$el.style.setProperty(`--${key}`, this.css_variables[key]);
        }
      }
    }
  }
</script>

<style lang="less">
  :root {
    --table_comp_font_family: Verdana,serif;
    --table_comp_title_font_size: 1rem;
    --table_comp_title_color: black;

    --table_comp_thead_color: black;
    --table_comp_thead_border_bottom: 1px solid black;
    --table_comp_thead_background: linear-gradient(to bottom, #f5f6f6 0%,#dbdce2 21%,#b8bac6 49%,#dddfe3 80%,#f5f6f6 100%);
    --table_comp_thead_text_align: left;

    --table_comp_row_color: black;
    --table_comp_row_selected_color: green;
    --table_comp_row_border_bottom: 1px solid black;
    --table_comp_row_odd_background: linear-gradient(to bottom, #cedce7 0%,#596a72 100%);
    --table_comp_row_even_background: linear-gradient(to bottom, #cedce7 0%,#596a72 100%);
    --table_comp_row_hover_color: linear-gradient(to bottom, #b4e391 0%,#61c419 50%,#b4e391 100%);
    --table_comp_cell_font_size: .75rem;
  }
  .tableComp {
    display: flex;
    flex-direction: column;
    font-family: var(--table_comp_font_family);

    &_title {
      font-size: var(--table_comp_title_font_size);
      color: var(--table_comp_title_color);
      font-weight: bold;
      align-self: center;
      margin-bottom: .8rem;
    }

    &_table {
      border-collapse: collapse;
    }

    &_thead {
      tr {
        display: block;
        th {
          color: var(--table_comp_thead_color);
          border-bottom: var(--table_comp_thead_border_bottom);
          background: var(--table_comp_thead_background);
          padding: 2px;
          cursor: pointer;
        }
      }
    }

    &_tbody {
      display: block;
      overflow-y: auto;
      overflow-x: hidden;
      &::-webkit-scrollbar-track {
        -webkit-box-shadow: inset 0 0 6px rgba(0,0,0,0.3);
        border-radius: 10px;
        background-color: #F5F5F5;
      }
      &::-webkit-scrollbar {
        height: 12px;
        width: 12px;
        background-color: transparent;
      }
      &::-webkit-scrollbar-thumb {
        border-radius: 10px;
        -webkit-box-shadow: inset 0 0 6px rgba(0,0,0,.3);
        background-color: #D62929;
      }
      tr {
        cursor: pointer;
        border-bottom: var(--table_comp_row_border_bottom);

        &:nth-child(odd) {
          background: var(--table_comp_row_odd_background);
        }
        &:nth-child(even) {
          background: var(--table_comp_row_even_background);
        }
        &:hover {
          background: var(--table_comp_row_hover_color);
        }
        td {
          font-size: var(--table_comp_cell_font_size);
          padding: 2px;
          text-align: center;
        }
      }
    }

    &_tr__selected {
      color: var(--table_comp_row_selected_color);
    }
    &_tr__notselected {
      color: var(--table_comp_row_color)
    }
  }
</style>