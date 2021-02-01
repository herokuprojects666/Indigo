<template>
  <div>
    <div @click="setIsExpanded()" v-bind:style="{width: '75%', float: 'right'}">{{ isExpanded ? "Click me to close" : "Click me to expand" }}</div>
    <div v-if="isExpanded">
      <div v-for="category in initialCategories" :key="category.name">
        <div v-bind:style="{width: '25%', float: 'left'}" v-if="!selectedCategory" @click="setSelectedCategory(category)">{{ category.name }}</div>
        <!-- view for when a category and menu has been selected. Nested children that get selected that don't have any menus skip setting selectedMenu-->
        <div v-if="selectedCategory && selectedMenu && menus.get(selectedMenu).childRefs.length">
          <div v-bind:style="{width: '75%', float: 'right'}" v-if="reverseLookups.has(selectedMenu)" @click="setIsOpen()">{{ reverseLookups.get(selectedMenu) }}</div>
          <div v-bind:style="{width: '25%', float: 'left'}" @click="setSelectedCategory(selectedCategory)">{{ selectedCategory }}</div>
          <div v-if="isOpen">
            <div v-bind:style="{width: '75%', float: 'right'}" @click="setSelectedMenu(menu)" v-for="menu in menus.get(selectedMenu).childRefs" :key="menu">
              {{ menus.get(menu).displayValue}}
            </div>
          </div>
        </div>
        <!-- view for when clicking on a main category -->
        <div v-if="selectedCategory && !selectedMenu">
          <div v-bind:style="{width: '25%', float: 'left'}" @click="setSelectedCategory(selectedCategory)">{{ selectedCategory }}</div>
          <div v-bind:style="{width: '75%', float: 'right'}" @click="setSelectedMenu(menu)" v-for="menu in mainList" :key="menu">
            {{ menus.get(menu).displayValue}}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
// Quick description: Elements on the far left represent the primary categories. I only included a single primary category but there could obviously be many.
// Each category has a list of initial references. These references are located in menus Map and have a value of { displayValue: string, childRefs: [other list of menus]}.
// In this way, any menu can have it's own menus which can have it's own menus and so on. We are essentially using the Map object to act like a single linked list.
// Per the requirements in the email, I was expected to spend 2-3 hours on this and this is how far I got after not having done anything complicated in Vue in the better part
// of 3 years. I believe all the actual functionality is complete. The only thing that I didn't get to is any sort of styling.
export default {
  name: 'Indigo',
  data: function() {
    return {
      initialCategories: [
        {
          name: 'cat1',
          initial: ['list1', 'list2']
        }
      ],
      isExpanded: false,
      isOpen: true,
      mainlist: [],
      menus: new Map(),
      reverseLookups: new Map(),
      selectedCategory: '',
      selectedMenu: ''
    }
  },
  methods: {
    setIsExpanded() {
      this.isExpanded = !this.isExpanded;
    },
    setIsOpen() {
      this.isOpen = !this.isOpen;
    },
    setSelectedCategory: function(category) {
      this.selectedMenu = '';
      // This method can be given a full category or just the currently selected one. If it's the currently selected one, treat that like a close and display
      // the primary list for the corresponding category
      if (typeof category === 'string' && category === this.selectedCategory) {
        this.isOpen = false;
        return;
      }
      this.selectedCategory = category.name;
      this.mainList = category.initial;
    },
    setSelectedMenu: function(menu) {
      this.isOpen = true;
      const child = this.menus.get(menu);
      // No more nested menus so don't do anything
      if (!child.childRefs.length) {
        return;
      }
      this.selectedMenu = menu
    }
  },
  created: function() {
    // Setting up all initially mapped values. I would normally expect this to come from some api endpoint reading values from a db to make it as generic as possible
    const subList2 = {
      displayValue: 'nested item',
      childRefs: []
    }
    const differentList1 = {
      displayValue: 'something something danger zone',
      childRefs: []
    }
    const differentList2 = {
      displayValue: 'Archer!!!!!',
      childRefs: [
        'malory',
        'figgus'
      ]
    }
    const differentList3 = {
      displayValue: 'robot unicorn',
      childRefs: []
    }
    const malory = {
      displayValue: 'Malory Arhcer',
      childRefs: []
    }
    const figgus = {
      displayValue: 'Figgus',
      childRefs: []
    }
    this.menus.set('list1', {
      displayValue: 'main list 1',
      childRefs: [
        'sublist2'
      ]
    })
    this.menus.set('list2', {
      displayValue: 'main list 2',
      childRefs: [
        'differentList1',
        'differentList2',
        'differentList3'
      ]
    })
    this.menus.set('sublist2', subList2);
    this.menus.set('differentList1', differentList1)
    this.menus.set('differentList2', differentList2)
    this.menus.set('differentList3', differentList3)
    this.menus.set('malory', malory)
    this.menus.set('figgus', figgus)
    this.reverseLookups.set('list1', this.menus.get('list1').displayValue);
    this.reverseLookups.set('list2', this.menus.get('list2').displayValue);
    this.reverseLookups.set('differentList1', this.menus.get('differentList1').displayValue)
    this.reverseLookups.set('differentList2', this.menus.get('differentList2').displayValue)
    this.reverseLookups.set('differentList3', this.menus.get('differentList3').displayValue)
  }
}
</script>