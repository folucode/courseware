<template>
    <div>
        <h2><u>Welcome to:</u> {{ course.name }}</h2>
        <p><u><b>Course description:</b></u><br> {{ course.description }}</p>
        <p><u><b>Tutor:</b></u> {{ course.tutor }}</p>

    </div>
</template>
  
<script>
export default {
    props: {
        course: Object,
    },
};
</script>
  