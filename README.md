# vue-bootstrap-pagination
Pagination for vuejs with bootstrap

## Installation
```
copy pagination.vue in your vue components
```

## Example

### js
```js
export default {
        data() {
            return {
                data: [],
                total: 0,
                page: 1,
                pagesize: 25
            }
        },
        methods: {
            loadData() {
                $.ajax({
                    url: '/users',
                    data: {
                        page: this.page,
                        pagesize: this.pagesize,
                    },
                    success(res) {
                        this.total = res.total;
                        this.data = res.data;
                    }
                });
            },
            changePage(page) {
                this.page = page;
                this.updateUrl();
            },
            updateUrl() {
                this.loadData();
                this.$router.push({
                    name: 'users.list',
                    query: {
                        page: this.page,
                        pagesize: this.pagesize
                    }
                });
            }
        },
        beforeCreate() {
            this.page = parseInt(this.$route.query.page || this.page);
            this.pagesize = parseInt(this.$route.query.pagesize || this.pagesize);
        },
        created() {
            this.loadData();
        },
        components: {
            pagination: require('./components/pagination.vue')
        }
    }
```

### html

```html
<pagination :total="total" :page="page" :pagesize="pagesize" @change-page="changePage"></pagination>
```

## Props
| Name          | Type     | Default | Required | Description
| :------------ | :--------| :-------| :--------| :-----------
| page          | Number   |         | true     | Current page number
| pagesize      | Number   |         | true     | Page size
| total         | Number   |         | true     | Total items


## Events
| Name          | Params     | Description
| :------------ | :--------  | :-----------
| change-page   | page       | Change page callback

