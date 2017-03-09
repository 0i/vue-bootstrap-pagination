<template>
    <nav aria-label="...">
        <ul v-if="total_pages > 1" class="pagination">
            <li v-if="page > 1">
                <a href="javascript:;" @click="changePage(1)" class="previous">{{ options.first }}</a>
            </li>
            <li v-else class="disabled">
                <a href="javascript:;" class="previous-off">{{ options.first }}</a>
            </li>

            <li v-if="page > 1">
                <a href="javascript:;" @click="changePage(page-1)" class="previous">{{ options.prev }}</a>
            </li>
            <li v-else class="disabled">
                <a href="javascript:;" class="previous-off">{{ options.prev }}</a>
            </li>

            <template v-for="(content, number) in links">
                <li v-if="number == page" class="active">
                    <a href="javascript:;" v-html="content"></a>
                </li>
                <li v-else>
                    <a href="javascript:;" @click="changePage(number)" v-html="content"></a>
                </li>
            </template>

            <li v-if="page < total_pages">
                <a href="javascript:;" @click="changePage(page + 1)" class="next">{{ options.next }}</a>
            </li>
            <li v-else class="disabled">
                <a href="javascript:;" class="next-off">{{ options.next }}</a>
            </li>

            <li v-if="page < total_pages">
                <a href="javascript:;" @click="changePage(total_pages)" class="next">{{ options.last }}</a>
            </li>
            <li v-else class="disabled">
                <a href="javascript:;" class="next-off">{{ options.last }}</a>
            </li>
        </ul>
    </nav>
</template>

<script>
    export default {
        props: {
            page: {
                type: Number,
                required: true,
                validator: value => value > 0
            },
            pagesize: {
                type: Number,
                required: true,
                validator: value => value > 0
            },
            total: {
                type: Number,
                required: true,
                validator: value => value >= 0
            },
            options: {
                type: Object,
                default: () => {
                    return {
                        prev: 'Previous',
                        next: 'Next',
                        first: 'First',
                        last: 'Last'
                    }
                }
            }
        },
        methods: {
            changePage(page) {
                page = parseInt(page);
                if (this.page === page) {
                    return;
                }
                
                this.$emit('change-page', page);
            }
        },
        computed: {
            total_pages() {
                return Math.ceil(this.total / this.pagesize);
            },
            links() {
                /*
                 First Previous 1 2 3 ... 22 23 24 25 26 [27] 28 29 30 31 32 ... 48 49 50 Next Last
                 */

                // Number of page links in the begin and end of whole range
                const count_out = 3;
                // Number of page links on each side of current page
                const count_in = 5;

                // Beginning group of pages: $n1...$n2
                const n1 = 1;
                const n2 = Math.min(count_out, this.total_pages);

                // Ending group of pages: $n7...$n8
                const n7 = Math.max(1, this.total_pages - count_out + 1);
                const n8 = this.total_pages;

                // Middle group of pages: $n4...$n5
                const n4 = Math.max(n2 + 1, this.page - count_in);
                const n5 = Math.min(n7 - 1, this.page + count_in);
                const use_middle = n5 >= n4;

                // Point $n3 between $n2 and $n4
                const n3 = parseInt((n2 + n4) / 2);
                const use_n3 = (use_middle && ((n4 - n2) > 1));

                // Point $n6 between $n5 and $n7
                const n6 = parseInt((n5 + n7) / 2);
                const use_n6 = (use_middle && ((n7 - n5) > 1));

                // Links to display as array(page => content)
                let links = {};

                // Generate links data in accordance with calculated numbers
                for (let i = n1; i <= n2; i++) {
                    links[i] = i;
                }
                if (use_n3) {
                    links[n3] = '&hellip;';
                }
                for (let i = n4; i <= n5; i++) {
                    links[i] = i;
                }
                if (use_n6) {
                    links[n6] = '&hellip;';
                }
                for (let i = n7; i <= n8; i++) {
                    links[i] = i;
                }
                
                return links;
            }
        }
    }
</script>
