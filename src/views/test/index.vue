<template>
    <div>
        <el-button type="primary" @click="dialogVisible = true">open</el-button>
        <el-dialog title="提示" :visible.sync="dialogVisible" width="60%">
            <div class="total-container">
                <div class="cross-container"
                    :style="{ transform: `translate(${left}px, ${top}px) rotate(${angle}deg)` }" @mousedown="startDrag"
                    @mousemove="drag" @mouseup="endDrag" @mouseleave="endDrag">
                    <img :src="imageSrc" alt="Draggable Image" style="width: 100px; height: 100px;" ref="image" />
                    <div class="rotate-handle" @mousedown="startRotate"><svg t="1712218531988" class="icon"
                            viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" p-id="8403"
                            width="16" height="16">
                            <path
                                d="M96.4 116.9l80.6 80.6c80.7-81.3 192.5-131.8 316.1-132 246.2-0.5 447.3 199.7 447.7 445.9C941.2 758.5 741 959 494 959c-131.9 0-250.4-57.1-332.2-148-37.2-41.4-7.9-107.4 47.8-107.9 18.5-0.2 36.2 7.5 48.5 21.2 58.1 64.5 142.3 105.1 235.9 105.1 176.1 0 318.8-143.5 317.3-320-1.5-176.2-145.1-316.6-321.4-314.4-86.6 1.1-164.8 36.9-221.4 94.1l79.6 79.6c11.8 11.8 3.4 31.9-13.2 31.9H83.2c-10.3 0-18.7-8.3-18.7-18.7V130.1c0-16.6 20.1-25 31.9-13.2z"
                                fill="#ffffff" p-id="8404"></path>
                        </svg></div>
                </div>
            </div>

            <span slot="footer" class="dialog-footer">
                <el-button @click="dialogVisible = false">取 消</el-button>
                <el-button type="primary" @click="sendInfo">确 定</el-button>
            </span>
        </el-dialog>
    </div>
</template>

<script>
export default {
    data() {
        return {
            left: 0,
            top: 0,
            startX: 0,
            startY: 0,
            dragging: false,
            rotating: false,
            angle: 0,
            imageSrc: require('./cross.png'),
            dialogVisible: false,
        };
    },
    methods: {
        startDrag(event) {
            if (!this.rotating) {
                this.startX = event.clientX - this.left;
                this.startY = event.clientY - this.top;
                this.dragging = true;
            }
        },
        drag(event) {
            if (this.dragging && !this.rotating) {
                this.left = event.clientX - this.startX;
                this.top = event.clientY - this.startY;
            }
        },
        endDrag() {
            this.dragging = false;
        },
        startRotate() {
            this.rotating = true;
            document.addEventListener('mousemove', this.rotate);
            document.addEventListener('mouseup', this.endRotate);
        },
        rotate(event) {
            if (this.rotating) {
                const center = {
                    x: this.left + this.$refs.image.offsetWidth / 2,
                    y: this.top + this.$refs.image.offsetHeight / 2
                };
                const deltaX = event.clientX - center.x;
                const deltaY = event.clientY - center.y;
                let newAngle = Math.atan2(deltaY, deltaX) * (180 / Math.PI);
                if (newAngle < 0) {
                    newAngle += 360; // 将负角度转换为正角度
                }
                this.angle = newAngle;
            }
        },
        endRotate() {
            this.rotating = false;
            document.removeEventListener('mousemove', this.rotate);
            document.removeEventListener('mouseup', this.endRotate);
        },
        sendInfo() {
            console.log('left: ' + this.left)
            console.log('top: ' + this.top)
            console.log('angle: ' + this.angle)
        }
    }
};
</script>

<style>
.cross-container {
    position: absolute;
    cursor: move;
    width: 100px;
    height: 100px;
}

.rotate-handle {
    position: absolute;
    top: 50%;
    left: 120%;
    transform: translate(-50%, -50%);
    width: 20px;
    height: 20px;
    /* background-color: #ffffff; */
    border-radius: 50%;
    cursor: ew-resize;
}

.total-container {
    height: 695px;
    width: 695px;
    background-image: url('tooth.png');
    background-size: cover;
    /* background-position: center */
}
</style>