<template>
    <div class="container">
        <label :class="{'transparent-bg': imageUrl}" class="drop-zone" for="file">
            <img :src="imageUrl" alt="Upload" style="height: 100%" v-if="imageUrl"/>
            <svg height="96" style=" fill:#000000;" v-else viewBox="0 0 172 172"
                 width="96" x="0px"
                 xmlns="http://www.w3.org/2000/svg"
                 y="0px">
                <g fill="none" fill-rule="nonzero" font-family="none" font-size="none" font-weight="none"
                   stroke="none" stroke-dasharray="" stroke-dashoffset="0" stroke-linecap="butt"
                   stroke-linejoin="miter" stroke-miterlimit="10" stroke-width="1" style="mix-blend-mode: normal"
                   text-anchor="none">
                    <path d="M0,172v-172h172v172z" fill="none"></path>
                    <path d="M0,86c0,-47.49649 38.50351,-86 86,-86c47.49649,0 86,38.50351 86,86c0,47.49649 -38.50351,86 -86,86c-47.49649,0 -86,-38.50351 -86,-86zM86,168.56c45.59663,0 82.56,-36.96337 82.56,-82.56c0,-45.59663 -36.96337,-82.56 -82.56,-82.56c-45.59663,0 -82.56,36.96337 -82.56,82.56c0,45.59663 36.96337,82.56 82.56,82.56z"
                          fill="#000000"></path>
                    <g fill="#000000">
                        <path d="M86,39.70333l-22.50943,30.69468h14.32418v36.83362h16.3705v-36.83362h14.32418zM36.88851,127.69475v8.18525h98.22298v-8.18525z"></path>
                    </g>
                    <path d="M86,172c-47.49649,0 -86,-38.50351 -86,-86v0c0,-47.49649 38.50351,-86 86,-86v0c47.49649,0 86,38.50351 86,86v0c0,47.49649 -38.50351,86 -86,86z"
                          fill="none"></path>
                    <path d="M86,168.56c-45.59663,0 -82.56,-36.96337 -82.56,-82.56v0c0,-45.59663 36.96337,-82.56 82.56,-82.56v0c45.59663,0 82.56,36.96337 82.56,82.56v0c0,45.59663 -36.96337,82.56 -82.56,82.56z"
                          fill="none"></path>
                    <path d="M86,172c-47.49649,0 -86,-38.50351 -86,-86v0c0,-47.49649 38.50351,-86 86,-86v0c47.49649,0 86,38.50351 86,86v0c0,47.49649 -38.50351,86 -86,86z"
                          fill="none"></path>
                    <path d="M86,168.56c-45.59663,0 -82.56,-36.96337 -82.56,-82.56v0c0,-45.59663 36.96337,-82.56 82.56,-82.56v0c45.59663,0 82.56,36.96337 82.56,82.56v0c0,45.59663 -36.96337,82.56 -82.56,82.56z"
                          fill="none"></path>
                </g>
            </svg>
        </label>
        <input @change="changeImage" class="hidden-file-input" id="file" name="file" type="file"/>

        <button :disabled="!image" @click="retrievePlateNumber" class="btn">Retrieve Plate Number</button>

        <div :class="{'flex': plateNumber}" @click="clickPlateNumberModal" class="modal" id="plate-number-modal"
             ref="plateNumberModal">
            <div class="modal-content">
                <h1 style="text-align: center">{{plateNumber}}</h1>
            </div>
        </div>
        <div :class="{'flex': error}" @click="clickErrorModal" class="modal" id="error-modal" ref="errorModal">
            <div class="modal-content">
                <h5 style="text-align: center; color: red">Could not recognize plate number</h5>
            </div>
        </div>
        <div :class="{'flex': loading}" class="modal" id="loader-modal">
            <div class="modal-content">
                <div class="loader"/>
            </div>
        </div>
    </div>
</template>

<script>

    export default {
        name: 'App',
        components: {},
        data: () => ({
            image: '',
            imageUrl: '',
            plateNumber: '',
            loading: false,
            error: false
        }),
        methods: {
            clickPlateNumberModal: function (event) {
                if (event.target === this.$refs.plateNumberModal)
                    this.plateNumber = ''
            },
            clickErrorModal: function (event) {
                if (event.target === this.$refs.errorModal)
                    this.error = '';
            },
            changeImage: function (event) {
                if (event.target.files && event.target.files[0]) {
                    this.image = event.target.files[0];
                    this.imageUrl = URL.createObjectURL(this.image);
                    this.plateNumber = '';
                }
            },
            retrievePlateNumber: function (event) {
                this.loading = true;
                const formData = new FormData()
                formData.append('image', this.image)
                fetch('https://anpr-core.herokuapp.com/anpr/', {
                    method: 'POST',
                    body: formData,
                })
                    .then(res => {
                        res.json().then(data => {
                            if (data['plate_number'])
                                this.plateNumber = data['plate_number']
                            else
                                this.error = true
                        });
                    })
                    .catch(() => this.error = true)
                    .finally(() => this.loading = false)
            }
        },
        mounted() {

        }
    }
</script>

<style scoped>
    .container {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        height: 100vh;
        box-sizing: border-box;
        top: 0
    }

    .hidden-file-input {
        width: .1px;
        height: .1px;
        opacity: 0;
        overflow: hidden;
        position: absolute;
        z-index: -1;
        box-sizing: border-box;
    }

    .drop-zone {
        display: flex;
        width: 600px;
        height: 50%;
        align-items: center;
        justify-content: center;
        box-sizing: border-box;
        background-color: rgba(184, 184, 184, 0.74);
        border-radius: 5px;
    }

    .btn {
        margin-top: 50px;
        padding: 20px;
        background-color: green;
        outline: none;
        border: 5px;
        color: white;
        font-weight: bold;
        font-size: 1.2em;

        border-radius: 5px;
    }

    .btn:focus {
        outline: none;
    }

    .transparent-bg {
        background-color: transparent;
    }

    .modal {
        display: none;
        justify-content: center;
        align-items: center;
        position: fixed;
        z-index: 2;
        left: 0;
        top: 0;
        width: 100%;
        height: 100%;
        overflow: auto;
        background-color: rgb(0, 0, 0);
        background-color: rgba(0, 0, 0, 0.4);
    }

    .modal-content {
        display: flex;
        background-color: #fefefe;
        margin: auto;
        padding: 20px;
        border: 1px solid #888;
        width: 20%;
        border-radius: 5px;
        justify-content: center;
    }

    .flex {
        display: flex;
    }

    .loader {
        /*border: 8px solid #f3f3f3; !* Light grey *!*/
        border: 8px solid #000000; /* Blue */
        border-top: 8px solid transparent; /* Blue */
        border-radius: 50%;
        width: 60px;
        height: 60px;
        animation: spin 1s linear infinite;
    }

    @keyframes spin {
        0% {
            transform: rotate(0deg);
        }
        100% {
            transform: rotate(360deg);
        }
    }
</style>
