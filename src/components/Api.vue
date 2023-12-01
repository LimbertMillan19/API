<script setup>

import { onMounted } from 'vue';
import dayjs from 'dayjs';
import { ref, computed } from 'vue';

dayjs.locale('es');

const info = ref({
    periodo: '',
    alTotal: '',
    alEvaluados: '',
    inicio: '',
    fin: '',
});

const info2 = ref({
    ISC: '',
    IEM: '',
    IER: '',
    IE: '',
    II: '',
})

async function fetchData() {
    try {
        const response = await fetch('https://sitmotul.com.mx/api/statusEval');
        const data = await response.json();
        info.value = data;
    } catch (error) {
        console.error('Error al obtener datos:', error);
    }
}

async function fetchDatas() {
    try {
        const response = await fetch('https://sitmotul.com.mx/api/statusEvalIng');
        const data = await response.json();
        info2.value = data;
        console.log(info2.value)
        // Update info2 or any other reactive variables here if needed
    } catch (error) {
        console.error('Error al obtener datos:', error);
    }
}

onMounted(async () => {
    await Promise.all([fetchData(), fetchDatas()]);
});


const formatoFechaInicio = computed(() => {
    return dayjs(info.value.inicio).format('DD-MM-YYYY');
});

const formatoFechaFin = computed(() => {
    return dayjs(info.value.fin).format('DD-MM-YYYY');
});


const diasrestantes = computed(() => {
    const fnow = dayjs();
    const ffin = dayjs(info.value.fin);

    const diferencia = ffin.diff(fnow, 'days');

    return diferencia
});



// Chart Js
const barpercent = computed(() => {
    const evaluados = info.value.alEvaluados;
    const todos = info.value.alTotal;
    return Math.round((evaluados / todos) * 100);
});


const barpercent2 = computed(() => {
    const evaluados = info2.value.ISC.listas;
    const todos = info2.value.ISC.faltantes + evaluados;
    return Math.round((evaluados / todos) * 100);
});

const barpercent3 = computed(() => {
    const evaluados = info2.value.IEM.listas;
    const todos = info2.value.IEM.faltantes + evaluados;
    return Math.round((evaluados / todos) * 100);
});

const barpercent4 = computed(() => {
    const evaluados = info2.value.IER.listas;
    const todos = info2.value.IER.faltantes + evaluados;
    return Math.round((evaluados / todos) * 100);
});

const barpercent5 = computed(() => {
    const evaluados = info2.value.IE.listas;
    const todos = info2.value.IE.faltantes + evaluados;
    return Math.round((evaluados / todos) * 100);
});

const barpercent6 = computed(() => {
    const evaluados = info2.value.II.listas;
    const todos = info2.value.II.faltantes + evaluados;
    return Math.round((evaluados / todos) * 100);
});





</script>






<template>
    <div class="bg-red-200 rounded-md  m-2 py-2 px-5 flex flex-col md:w-[50%] w-full justify-center items-center">
        <h1 class="mx-5 mt-10 text-3xl text-center font-bold pb-5 ">SITMOTUL</h1>
        <img class="  w-[10%]" src="./logoSitmotul-58d3b94c.svg" alt="">
        <p class=" pt-5 font-bold text-center mb-5">Estado de la evaluación tutor del <span>{{ info.periodo }}</span></p>

        <p class="font-bold text-center">Del <span>{{ formatoFechaInicio }}</span></p>
        <p class="font-bold text-center">Al <span>{{ formatoFechaFin }}</span></p>


        <div class=" flex w-full bg-amber-400 mt-5 rounded-md">

            <div class="text-center w-full">
                <p class="text-sm">Quedan:</p>
                <p class="font-bold text-3xl">{{ diasrestantes }}</p>
                <p><span class="text-sm"> Días restantes</span></p>
            </div>

        </div>

        <div class="flex space-x-2">

            <div class="flex justify-center bg-blue-400 mt-2 rounded-md w-full">
                <div class="text-center  ">
                    <p>Hay:</p>
                    <p class="font-bold text-3xl md:text-5xl">{{ info.alEvaluados }}</p>
                    <p><span> Evaluaciones realizadas</span></p>
                </div>
            </div>

            <div class=" flex bg-red-500 mt-2 rounded-md w-full justify-center">

                <div class="text-center">
                    <p>Faltan:</p>
                    <p class="font-bold text-3xl md:text-5xl">{{ info.alTotal - info.alEvaluados }}</p>
                    <p><span> Alumnos por terminar la evaluación</span></p>
                </div>

            </div>


        </div>

        <!-- Bar -->

        <div class="bg-green-400 flex fjustify-center rounded-md items-center py-8 mt-2">

            <div class="flex justify-center flex-col items-center  w-[20%]">

                <p class="md:text-3xl font-bold">{{ Math.round((info.alEvaluados / info.alTotal) * 100) }}%</p>
                <p class="md:text-base text-xs">Realizadas</p>

            </div>


            <div class=" w-[60%]">
                <div class="w-full max-w-md mx-auto ">
                    <div class="bg-gray-200 rounded-lg overflow-hidden">
                        <div class="bg-blue-500 text-white py-2 px-4 text-center" :style="{ width: `${barpercent}%` }">
                            {{ barpercent }}%
                        </div>
                    </div>
                </div>
            </div>

            <div class="flex justify-center flex-col items-center  w-[20%]">
                <p class="md:text-3xl font-bold">{{ Math.round(100 - (info.alEvaluados / info.alTotal) * 100) }}%</p>
                <p class="md:text-base text-xs">Restantes</p>

            </div>


        </div>



        <div class=" my-10 pb-10  bg-violet-300 rounded-md">

            <p class="text-2xl font-bold text-center py-3">Porcentajes en todas las carreras</p>

            <div class="flex flex-col justify-center items-center">

                <div class="bg-violet-500 w-[90%] flex flex-col justify-center rounded-md items-center py-2 mt-2">

                    <p class="pb-5 font-bold text-xl">ISC</p>

                    <div class="flex w-full">


                        <div class="flex justify-center flex-col items-center  w-[20%]">

                            <p class="md:text-3xl font-bold">{{ barpercent2 }}%</p>
                            <p class="md:text-base text-xs">Realizadas</p>

                        </div>


                        <div class=" w-[60%]">
                            <div class="w-full max-w-md mx-auto ">
                                <div class="bg-gray-200 rounded-lg overflow-hidden">
                                    <div class="bg-blue-500 text-white py-2 px-4 text-center"
                                        :style="{ width: `${barpercent2}%` }">
                                        {{ barpercent2 }}%
                                    </div>
                                </div>
                            </div>
                        </div>

                        <div class="flex justify-center flex-col items-center  w-[20%]">
                            <p class="md:text-3xl font-bold">{{ Math.round(100 - (barpercent2)) }}%</p>
                            <p class="md:text-base text-xs">Restantes</p>

                        </div>
                    </div>


                </div>


                <div class="bg-violet-500 w-[90%] flex flex-col justify-center rounded-md items-center py-2 mt-5">

                    <p class="pb-5 font-bold text-xl">IEM</p>

                    <div class="flex w-full">


                        <div class="flex justify-center flex-col items-center  w-[20%]">

                            <p class="md:text-3xl font-bold">{{ barpercent3 }}%</p>
                            <p class="md:text-base text-xs">Realizadas</p>

                        </div>


                        <div class=" w-[60%]">
                            <div class="w-full max-w-md mx-auto ">
                                <div class="bg-gray-200 rounded-lg overflow-hidden">
                                    <div class="bg-blue-500 text-white py-2 px-4 text-center"
                                        :style="{ width: `${barpercent3}%` }">
                                        {{ barpercent3 }}%
                                    </div>
                                </div>
                            </div>
                        </div>

                        <div class="flex justify-center flex-col items-center  w-[20%]">
                            <p class="md:text-3xl font-bold">{{ Math.round(100 - (barpercent3)) }}%</p>
                            <p class="md:text-base text-xs">Restantes</p>

                        </div>
                    </div>


                </div>

                <div class="bg-violet-500 w-[90%] flex flex-col justify-center rounded-md items-center py-2 mt-5">

                    <p class="pb-5 font-bold text-xl">IER</p>

                    <div class="flex w-full">


                        <div class="flex justify-center flex-col items-center  w-[20%]">

                            <p class="md:text-3xl font-bold">{{ barpercent4 }}%</p>
                            <p class="md:text-base text-xs">Realizadas</p>

                        </div>


                        <div class=" w-[60%]">
                            <div class="w-full max-w-md mx-auto ">
                                <div class="bg-gray-200 rounded-lg overflow-hidden">
                                    <div class="bg-blue-500 text-white py-2 px-4 text-center"
                                        :style="{ width: `${barpercent4}%` }">
                                        {{ barpercent4 }}%
                                    </div>
                                </div>
                            </div>
                        </div>

                        <div class="flex justify-center flex-col items-center  w-[20%]">
                            <p class="md:text-3xl font-bold">{{ Math.round(100 - (barpercent4)) }}%</p>
                            <p class="md:text-base text-xs">Restantes</p>

                        </div>
                    </div>


                </div>

                <div class="bg-violet-500 w-[90%] flex flex-col justify-center rounded-md items-center py-2 mt-5">

                    <p class="pb-5 font-bold text-xl">IE</p>

                    <div class="flex w-full">


                        <div class="flex justify-center flex-col items-center  w-[20%]">

                            <p class="md:text-3xl font-bold">{{ barpercent5 }}%</p>
                            <p class="md:text-base text-xs">Realizadas</p>

                        </div>


                        <div class=" w-[60%]">
                            <div class="w-full max-w-md mx-auto ">
                                <div class="bg-gray-200 rounded-lg overflow-hidden">
                                    <div class="bg-blue-500 text-white py-2 px-4 text-center"
                                        :style="{ width: `${barpercent5}%` }">
                                        {{ barpercent5 }}%
                                    </div>
                                </div>
                            </div>
                        </div>

                        <div class="flex justify-center flex-col items-center  w-[20%]">
                            <p class="md:text-3xl font-bold">{{ Math.round(100 - (barpercent5)) }}%</p>
                            <p class="md:text-base text-xs">Restantes</p>

                        </div>
                    </div>


                </div>

                <div class="bg-violet-500 w-[90%] flex flex-col justify-center rounded-md items-center py-2 mt-5">

                    <p class="pb-5 font-bold text-xl">II</p>

                    <div class="flex w-full">


                        <div class="flex justify-center flex-col items-center  w-[20%]">

                            <p class="md:text-3xl font-bold">{{ barpercent6 }}%</p>
                            <p class="md:text-base text-xs">Realizadas</p>

                        </div>


                        <div class=" w-[60%]">
                            <div class="w-full max-w-md mx-auto ">
                                <div class="bg-gray-200 rounded-lg overflow-hidden">
                                    <div class="bg-blue-500 text-white py-2 px-4 text-center"
                                        :style="{ width: `${barpercent6}%` }">
                                        {{ barpercent6 }}%
                                    </div>
                                </div>
                            </div>
                        </div>

                        <div class="flex justify-center flex-col items-center  w-[20%]">
                            <p class="md:text-3xl font-bold">{{ Math.round(100 - (barpercent6)) }}%</p>
                            <p class="md:text-base text-xs">Restantes</p>

                        </div>
                    </div>


                </div>


            </div>








        </div>


    </div>
</template>