<template>
    <div>
        <div class="p-10" v-for="(row,rowIndex) of box_data" :key="rowIndex">
            <span v-for="(col,colIndex) of row" :key="colIndex">
                <span
                    v-if="col"
                    style="transition: background-color 500ms ease-in-out; border: solid black 1px; border-radius: 5px; padding: 30px 50px; margin: 10px;"
                    :style="[selectionSet.has(getKey(rowIndex,colIndex)) ? {background: 'darkseagreen'} : {background: 'white'}]"
                    class="cursor-pointer"
                    @click="selectBox(rowIndex,colIndex)"
                >
                </span>
                <span v-else
                      style="padding: 30px 50px; margin: 10px;"
                ></span>
            </span>
        </div>
    </div>
</template>

<script>

export default {
    name: 'App',
    data() {
        return {
            disableAction: false,
            box_data: [
                [1, 1, 1],
                [1, 0, 0],
                [1, 1, 1],
            ],
            selectionSet: new Set()
        }
    },
    components: {},
    methods: {
        async unsetBox(key) {
            return new Promise((resolve, reject) => {
                setTimeout(() => {
                    this.selectionSet.delete(key);
                    resolve(key);
                }, 500)
            })
        },
        async selectBox(i, j) {
            if (this.disableAction) {
                return;
            }
            const key = this.getKey(i, j)
            if (this.selectionSet.has(key)) {
                return;
            } else {
                this.selectionSet.add(key);
            }

            // check if all selected

            let totalBoxes = 0;
            for (let i = 0; i < this.box_data.length; i++) {
                for (let j = 0; j < this.box_data[0].length; j++) {
                    if (this.box_data[i][j] == 1) {
                        totalBoxes++;
                    }
                }
            }

            if (this.selectionSet.size === totalBoxes) {
                this.disableAction = true;
                const boxesSelectedInOrder = Array.from(this.selectionSet);
                for (let i = 0; i < boxesSelectedInOrder.length; i++) {
                    await this.unsetBox(boxesSelectedInOrder[i]);
                }

                this.selectionSet = new Set();
                this.disableAction = false;
            }
        },
        getKey(i, j) {
            const key = `${i}_${j}`;
            return key;
        }
    }
}
</script>

<style>
#app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
}
</style>
