<template>
    <TeacherLayout>
        <div class="mt-12 flex gap-8">
            <div class="flex-1 border flex flex-col h-fit">
                <div class="pt-12 px-8 text-center flex justify-center flex-col w-full items-center">
                    <img src="https://static.vecteezy.com/system/resources/previews/009/385/472/non_2x/school-desk-clipart-design-illustration-free-png.png"
                        alt="" class="h-24 w-24 object-cover" />

                    <h3 class="font-semibold text-lg text-black my-2">
                        {{ projectInfo.projectName }}
                    </h3>
                    <div class="border w-full my-6"></div>
                </div>
                <div class="px-8">
                    <p class="uppercase text-[#a8aaae] font-medium text-sm">Details</p>
                    <div class="mt-4">
                        <span class="font-medium text-sm">Name: </span>
                        <span>{{ projectInfo.projectName }}</span>
                    </div>
                    <div class="mt-4">
                        <span class="font-medium text-sm">Description: </span>
                        <span>{{ projectInfo.description }}</span>
                    </div>
                    <div class="mt-4" v-if="projectInfo.isApproved == 'Đã xét duyệt'">
                        <span class="font-medium text-sm">Deadline: </span>
                        <span>{{ formattedDOB }}</span>
                    </div>
                    <div class="mt-4">
                        <span class="font-medium text-sm">State: </span>
                        <span>{{ projectInfo.isApproved }}</span>
                    </div>
                    <div v-if="projectInfo.type == 0">
                        <div class="mt-4">
                            <span class="font-medium text-sm">Execution Score: </span>
                            <span>{{ listMark.execution }}</span>
                        </div>
                    </div>
                    <div v-if="projectInfo.type == 1 && isExamination == true">
                        <div class="mt-4">
                            <span class="font-medium text-sm">Defense Score: </span>
                            <span>{{ listMark.sumDefense }}</span>
                        </div>
                    </div>
                    <div v-else-if="projectInfo.type == 1">
                        <div class="mt-4">
                            <span class="font-medium text-sm">Guidance Score: </span>
                            <span>{{ listMark.guidance }}</span>
                        </div>
                        <div class="mt-4">
                            <span class="font-medium text-sm">Execution Score: </span>
                            <span>{{ listMark.execution }}</span>
                        </div>
                        <div class="mt-4">
                            <span class="font-medium text-sm">Proccess Score: </span>
                            <span>{{ listMark.proccess }}</span>
                        </div>
                        <div class="mt-4">
                            <span class="font-medium text-sm">Defense Score: </span>
                            <span>{{ listMark.sumDefense }}</span>
                        </div>
                        <div class="mt-4">
                            <span class="font-medium text-sm">Final Project Score: </span>
                            <span>{{ listMark.final }}</span>
                        </div>
                        <div v-if="projectInfo.type == 1 && isDone == true">
                            <div class="mt-4">
                                <span class="font-medium text-sm">Result: </span>
                                <span>{{ isPass }}</span>
                            </div>
                        </div>
                    </div>
                    <div class="mt-4"></div>
                </div>
                <div class="flex gap-4 my-4 m-auto" v-if="projectInfo.isApproved == 'Chưa được chấp nhận' &&
                    !projectInfo.isDenied
                    ">
                    <UButton color="primary" variant="solid" size="xl" @click="isOpenAccept = true">Accept</UButton>
                    <UButton color="red" variant="outline" size="xl" @click="denyProject">Deny</UButton>
                </div>
                <div class="flex gap-4 my-4 m-auto" v-if="projectInfo.isApproved == 'Đã xét duyệt'">
                    <UButton color="primary" variant="solid" size="xl" @click="isOpenMark = true">Mark</UButton>
                    <UButton color="blue" variant="solid" size="xl" @click="isOpenViewMark = true">
                        <Icon name="material-symbols:visibility" class="text-white" />
                    </UButton>
                </div>
            </div>
            <div class="flex-[2]">
                <div class="w-full flex gap-4">
                    <div class="mt-4 px-4 py-8 border">
                        <h3 class="font-medium text-lg text-[#6f6b7d]">
                            View student's file
                        </h3>
                        <div v-if="file && file.fileUrl">
                            <template v-if="isPDF(file.fileName)">
                                <!-- Hiển thị PDF -->
                                <iframe :src="file.fileUrl" width="600px" height="500px" frameborder="0"></iframe>
                            </template>
                            <template v-else-if="isWordDoc(file.fileName)">
                                <!-- Hiển thị tên file và biểu tượng của Google Docs -->
                                <a :href="file.fileUrl" download="file.fileName">
                                    <button class="btn btn-primary" style="font-size: 16px">
                                        <span class="icon-text">
                                            <FileTextOutlined />
                                            <span class="ml-2">{{
                                                getShortFileName(file.fileName)
                                            }}</span>
                                        </span>
                                    </button>
                                </a>
                            </template>
                            <template v-else>
                                <p>This file format is not supported.</p>
                            </template>
                        </div>
                    </div>
                </div>
                <NuxtPage />
            </div>
            <template>
                <div>
                    <UModal v-model="isOpenAccept" prevent-close>
                        <UCard :ui="{
                            ring: '',
                            divide: 'divide-y divide-gray-100 dark:divide-gray-800',
                        }">
                            <template #header>
                                <div class="flex items-center justify-between">
                                    <h3 class="text-base font-semibold leading-6 text-gray-900 dark:text-white">
                                        Are you ready to approve this project
                                    </h3>
                                    <UButton color="gray" variant="ghost" icon="i-heroicons-x-mark-20-solid" class="-my-1"
                                        @click="isOpenAccept = false" />
                                </div>
                            </template>
                            <UForm :schema="schema" :state="state" @submit="submitAccept">
                                <UFormGroup class="mb-4 flex-1" label="Deadline of this task" name="deadline">
                                    <UInput v-model="state.deadline" type="date" />
                                </UFormGroup>
                                <UButton type="submit"> Submit </UButton>
                            </UForm>
                        </UCard>
                    </UModal>
                </div>
            </template>
            <template>
                <div>
                    <UModal v-model="isOpenMark" prevent-close>
                        <UCard :ui="{
                            ring: '',
                            divide: 'divide-y divide-gray-100 dark:divide-gray-800',
                        }">
                            <template #header>
                                <div class="flex items-center justify-between">
                                    <h3 class="text-base font-semibold leading-6 text-gray-900 dark:text-white">
                                        Mark {{ projectInfo.projectName }}
                                    </h3>
                                    <UButton color="gray" variant="ghost" icon="i-heroicons-x-mark-20-solid" class="-my-1"
                                        @click="isOpenMark = false" />
                                </div>
                            </template>
                            <UForm :schema="schema" :state="mark" @submit="submitMark">
                                <div class="flex gap-4">
                                    <UFormGroup class="mb-4 flex-1" label="Mark" name="mark">
                                        <UInput v-model="mark.mark" placeholder="Mark of project" type="number"
                                            step="0.1" />
                                    </UFormGroup>

                                    <UFormGroup class="mb-4 flex-1" label="Type" name="type"
                                        v-if="isExamination == false && projectInfo.type == 1">
                                        <USelect v-model="mark.type" :options="typeOptions.map((t) => ({
                                            label: t.type,
                                            value: t._id,
                                        }))
                                            " />
                                    </UFormGroup>
                                </div>
                                <UFormGroup class="mb-4 flex-1" label="Comment" name="comment">
                                    <UInput v-model="mark.comment" placeholder="Comment of project" />
                                </UFormGroup>
                                <UButton type="submit"> Submit </UButton>
                            </UForm>
                        </UCard>
                    </UModal>
                </div>
            </template>
            <template>
                <div>
                    <UModal v-model="isOpenViewMark" prevent-close>
                        <UCard :ui="{
                            ring: '',
                            divide: 'divide-y divide-gray-100 dark:divide-gray-800',
                        }">
                            <template #header>
                                <div class="flex items-center justify-between">
                                    <h3 class="text-base font-semibold leading-6 text-gray-900 dark:text-white">
                                        {{ projectInfo.projectName }}
                                    </h3>
                                    <UButton color="gray" variant="ghost" icon="i-heroicons-x-mark-20-solid" class="-my-1"
                                        @click="isOpenViewMark = false" />
                                </div>
                            </template>

                            <UTable :columns="columns" :rows="listViewMark" :sort="{ column: 'title' }">
                            </UTable>
                            <UButton @click="exportToPdf"> Export </UButton>
                        </UCard>
                    </UModal>
                </div>
            </template>
            <div style="display: none">
                <div id="pdfContent">
                    <MarkForm :listMark="responseMark" :projectName="projectInfo.projectName" :studentInfo="studentInfo" :className="className"></MarkForm>
                </div>
            </div>
        </div>
    </TeacherLayout>
</template>

<script setup lang="ts">
import TeacherLayout from '~/layouts/TeacherLayout.vue';
import MarkForm from './form.vue';
import axios from 'axios';
import { useToast } from 'vue-toast-notification';
import 'vue-toast-notification/dist/theme-sugar.css';
import type { FormSubmitEvent } from '@nuxt/ui/dist/runtime/types';
import * as z from 'zod';
import { ref, onMounted, defineProps } from 'vue';
 
const route = useRoute();
const toast = useToast();
const isOpenAccept = ref(false);
const isOpenMark = ref(false);
const isOpenViewMark = ref(false);
let isExamination = ref(false);

const projectInfo = ref({
    _id: '',
    projectName: '',
    description: '',
    isApproved: '',
    isDenied: '',
    listMark: '',
    date_created: '',
    deadline: '',
    type: 0,
});
const columns = [
    {
        key: 'type',
        label: 'Type',
        sortable: true,
    },
    {
        key: 'mark',
        label: 'Mark',
        sortable: true,
    },
    {
        key: 'comment',
        label: 'Comment',
        sortable: true,
    },
];
const schema = z.object({});

type Schema = z.output<typeof schema>;
const state = ref({
    deadline: '',
});
const mark = ref({
    mark: 0,
    type: 1,
    comment: '',
});
const typeOptions = [
    {
        type: 'Guidance',
        _id: 0,
    },
    {
        type: 'Execution',
        _id: 1,
    },
    {
        type: 'Defense',
        _id: 2,
    },
];
const listOfType = [
    {
        type: 'Guidance',
        _id: 0,
    },
    {
        type: 'Execution',
        _id: 1,
    },
    {
        type: 'Defense',
        _id: 2,
    },
    {
        type: 'Process',
        _id: 3,
    },
    {
        type: 'Sum of defense',
        _id: 4,
    },
    {
        type: 'Final',
        _id: 5,
    },
];
const listMark = ref<any>({
    guidance: 0,
    execution: 0,
    defense: 0,
    proccess: 0,
    sumDefense: 0,
    final: 0,
});
const responseMark = ref([]);
const listViewMark = ref([
    {
        mark: 0,
        type: 0,
        comment: '',
    },
]);
const studentInfo = ref();
const className = ref();
const file = ref<any>();
const isPass = ref<any>();
const isDone = ref<any>();
async function loadData() {
    try {
        const teacherId = localStorage.getItem('_id');
        const userId = route.params.userId;
        const id = route.params.id;
        const classId = route.params.classId;
        const response = await axios.get(
            `http://localhost:5000/api/project/${userId}/${id}`
        );
        const response_file = await axios.get(
            `http://localhost:5000/api/upload/${id}`
        );
        const response_class = await axios.get(
            `http://localhost:5000/api/class/getDetail/${classId}`
        );
        const response_mark = await axios.get(
            `http://localhost:5000/api/mark/${id}`
        );
        const response_user = await axios.get(`http://localhost:5000/api/user/getUserInfor/${userId}`);
        studentInfo.value = response_user.data.userData
        className.value = response_class.data.className

        if (teacherId != response_class.data.teacherId._id) {
            isExamination.value = true;
            mark.value.type = 2;
        }
        file.value = response_file.data[0];
        const projectData = response.data[0];

        if (response_mark.data && response_mark.data.length > 0) {
            const allTypesPresent = listOfType.every((typeObj) =>
                response_mark.data.some((markData) => markData.type === typeObj._id)
            );
            isDone.value = allTypesPresent;
            const isFinalMarkPass = response_mark.data.some((markData) => {
                return markData.type === 5 && markData.mark >= 5.5;
            });

            isPass.value = isFinalMarkPass ? 'Pass' : 'Fail';
        } else {
            isDone.value = false;
        }

        projectInfo.value = {
            ...projectData,
            isApproved: projectData.isApproved
                ? 'Đã xét duyệt'
                : 'Chưa được chấp nhận',
        };
        state.value.deadline = formattedDOB.value;
        responseMark.value = response_mark.data;
        console.log(responseMark)
        listViewMark.value = response_mark.data.map((markData) => ({
            ...markData,
            type: getTypeLabel(markData.type),
        }));
        response_mark.data.forEach((markData) => {
            switch (markData.type) {
                case 0:
                    listMark.value.guidance = markData.mark;
                    break;
                case 1:
                    listMark.value.execution = markData.mark;
                    if (projectInfo.value.type == 0) {
                        mark.value.mark = markData.mark;
                        mark.value.comment = markData.comment;
                    }
                    break;
                case 2:
                    if (markData.teacherId == teacherId && isExamination.value) {
                        listMark.value.defense = markData.mark;
                        mark.value.mark = markData.mark;
                        mark.value.comment = markData.comment;
                    }
                    break;
                case 3:
                    listMark.value.proccess = markData.mark;
                    break;
                case 4:
                    listMark.value.sumDefense = markData.mark;
                    break;
                case 5:
                    listMark.value.final = markData.mark;
                    break;
                default:
                    break;
            }
        });
    } catch (error) {
        console.error(error);
    }
}

async function submitAccept(event: FormSubmitEvent<Schema>) {
    try {
        event.data.isApproved = true;
        const id = route.params.id;
        const response = await axios.put(
            `http://localhost:5000/api/project/${id}`,
            event.data
        );
        if (response.data) {
            loadData();
            toast.success('You have approved this project');
            isOpenAccept.value = false;
        } else {
            toast.error('An error occurred during form submission.');
        }
    } catch (error) {
        toast.error('An error occurred during form submission.');
    }
}
async function submitMark(event: FormSubmitEvent<Schema>) {
    const projectId = route.params.id;
    const teacherId = localStorage.getItem('_id');
    const token = localStorage.getItem('token');
    const studentId = route.params.userId;
    const headers = {
        Authorization: `Bearer ${token}`,
        'Content-Type': 'application/json',
    };
    try {
        console.log(event.data);
        const selectedType = typeOptions.find((t) => t._id == event.data.type);
        if (selectedType) {
            event.data.type = selectedType._id;
        } else {
            event.data.type = 0;
        }
        event.data.mark = parseFloat(event.data.mark);
        console.log(event.data);
        const response_mark = await axios.post(
            `http://localhost:5000/api/mark/${teacherId}/${studentId}/${projectId}`,
            event.data,
            { headers }
        );
        if (response_mark.data.state == 0) {
            loadData();
            toast.success('Mark successfully');
            isOpenMark.value = false;
        } else if (response_mark.data.state == 1) {
            loadData();
            toast.success('Update mark successfully');
            isOpenMark.value = false;
        } else {
            toast.error('Mark fail');
        }
    } catch (error) {
        console.log(error);
        toast.error('An error occurred during form submission.');
    }
}
async function denyProject() {
    try {
        const id = route.params.id;
        const response = await axios.put(
            `http://localhost:5000/api/project/${id}`,
            {
                isDenied: true,
            }
        );
        if (response.data) {
            loadData();
            toast.success('You have denied this project');
        } else {
            toast.error('An error occurred during form submission.');
        }
    } catch (error) {
        console.error(error);
    }
}

const formattedDOB = computed(() => {
    console.log(projectInfo.value.deadline);
    const rawDOB = projectInfo.value.deadline;
    if (rawDOB) {
        const date = new Date(rawDOB);
        const year = date.getFullYear();
        const month = String(date.getMonth() + 1).padStart(2, '0');
        const day = String(date.getDate()).padStart(2, '0');
        return `${year}-${month}-${day}`;
    }
    return '';
});

function isPDF(fileName: string): boolean {
    return fileName.toLowerCase().endsWith('.pdf');
}

function isWordDoc(fileName: string): boolean {
    return (
        fileName.toLowerCase().endsWith('.doc') ||
        fileName.toLowerCase().endsWith('.docx')
    );
}

function getShortFileName(fileName: string): string {
    const maxLength = 20;
    if (fileName.length <= maxLength) {
        return fileName;
    }
    return fileName.substr(0, maxLength) + '...';
}
onMounted(loadData);

watch(
    () => mark.value.type,
    (newValue, oldValue) => {
        if (isExamination.value == false) {
            const selectedMark = responseMark.value.find(
                (markData) => markData.type == newValue
            );
            if (selectedMark) {
                mark.value.mark = selectedMark.mark;
                mark.value.comment = selectedMark.comment;
            } else {
                mark.value.mark = 0;
                mark.value.comment = '';
            }
        }
    }
);

function getTypeLabel(type) {
    return listOfType.find((t) => t._id === type)?.type || '';
}

async function exportToPdf() {
    const pdfContentElement = document.getElementById('pdfContent');
    const encodeHTML = encodeURIComponent(pdfContentElement?.outerHTML)
    try {
        const response = await axios.post(
            `http://localhost:5000/api/mark/exportToPDF`, { html_data: encodeHTML }
        );
        if (response.data) {
            toast.success(response.data.msg + " at " + response.data.data)
        }
    } catch (error) {
        toast.error(error)
        console.log(error)
    }
}
</script>
<style>
.icon-text {
    display: flex;
    align-items: center;
}

.ml-2 {
    margin-left: 0.5rem;
}
</style>
