<template>
    <b-container>
        <b-row class="mb-3">
            <b-col cols="8">
                <b-form-input
                    v-model="searchValue"
                    type="text"
                    placeholder="Buscar...">
                </b-form-input>
            </b-col>
            <b-col>
                <b-form-select v-model="searchParam" :options="filters"></b-form-select>
            </b-col>
        </b-row>
        
        <b-table striped hover 
            :items="countries"
            :sort-by.sync="sortBy"
            :sort-desc.sync="sortDesc"
            :fields="fields"
            :per-page="perPage"
            :current-page="currentPage"
            :select-mode="selectMode"
            @row-selected="onRowSelected"
            show-empty
            selectable>

            <template #cell(languages)="data">
                <a @click="showLanguages(data)"> View Languages </a>
            </template>

            <template #cell(flagsAsImage)="data">
                <img style="width: 50px;" :src="data.item.flags.svg" />
            </template>

            <template #empty="">
                No data to display
            </template>
        </b-table>

        <b-pagination
        v-model="currentPage"
        :total-rows="rows"
        :per-page="perPage"
        aria-controls="my-table">
        </b-pagination>
    </b-container>
</template>

<script>
import axios from 'axios';

/* Bootbox config */
const $ = require('jquery');
global.jQuery = require("jquery");
window.$ = $;
const bootbox = require('bootbox');

export default {
    data() {
      return {
        selectMode: 'single',
        searchValue: '',
        searchParam: '',
        sortBy: 'name.official',
        sortDesc: false,
        perPage: 10,
        currentPage: 1,
        fields: [
            { key: 'name.official', label: 'Official Name' },
            { key: 'capital[0]', label: 'Capital' },
            { key: 'region', label: 'Region' },
            { key: 'languages', label: 'Languages' },
            { key: 'population', label: 'Population' },
            { key: 'flagsAsImage', label: 'Flag' }
        ],
        filters: [
            { value: '', text: 'Select a filter' },
            { value: 'name', text: 'Official Name' },
            { value: 'capital', text: 'Capital' },
            { value: 'region', text: 'Region' },
            { value: 'population', text: 'Population' }
        ],
        items: []
      }
    },
    mounted() {
        this.getCountries();
    },
    computed: {
      rows() {
        return this.items.length;
      },
      countries() {
        if(this.searchValue.length > 3) {
            return this.items
                        .filter( item => {
                            if (this.searchParam === 'name') {
                                return item.name.official.toLowerCase().includes(this.searchValue.toLowerCase());
                            }
                            if (this.searchParam === 'capital' && item.capital != null) {
                                return item['capital'][0].toLowerCase().includes(this.searchValue.toLowerCase());
                            }
                            if (this.searchParam === 'population') {
                                return item.population.toString().includes(this.searchValue);
                            }
                            if (this.searchParam === 'region') {
                                return item.region.toString().includes(this.searchValue);
                            }

                            return true;
                        })
        }

        return this.items;
      }
    },
    methods: {
        getCountries () {
            axios
                .get("https://restcountries.com/v3.1/all")
                .then(response => {
                    console.log(response.data[0]);
                    this.items = response.data
                })
                .catch(function (error) {
                    console.log(error);
                })
        },
        onRowSelected(item) {
            const data = item[0];

            axios
            .get(`https://en.wikipedia.org/api/rest_v1/page/summary/${data.name.official}`)
            .then(response => {
                bootbox.alert(`${response.data.extract_html}`);
            })
            .catch(function (error) {
                console.log(error);
            })
        },
        showLanguages( {item} ) {
            if(item.languages != null) {
                let parsedItem = JSON.parse(JSON.stringify(item.languages))
                let languages = [];

                for (const language in parsedItem) {
                    languages.push(`${parsedItem[language]}`);
                }
                
                bootbox.alert(`${languages}`);
            }            
        }
    }

}
</script>

<style scoped>
    .pagination {
    display: flex;
    justify-content: center;
    }
</style>