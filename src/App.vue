<template lang="pug">
#app
  <!--.limit-->
    <!--span.remaining-characters 64-->
    <!--svg.counter(viewbox='0 0 33.83098862 33.83098862', height='20', width='20', xmlns='http://www.w3.org/2000/svg')-->
      <!--circle.underlay(cx='17', cy='17', r='16', fill='none', stroke-width='2')-->
      <!--circle.progress(stroke-dasharray='75,100', cx='17', cy='17', r='16', fill='none', stroke-width='4')-->
    // 2 * pi * r = stroke-dasharray
    // stroke-dasharray * (1 - %) = progress
    //.limit
    span.remaining-characters 64
    svg.counter(viewbox='0 0 40 40', height='40', width='40', xmlns='http://www.w3.org/2000/svg')
      circle.underlay(cx='40', cy='40', r='18', fill='none', stroke-width='0.5')
      circle.progress(stroke-dasharray='68.884, 106.814', cx='40', cy='40', r='17', fill='none', stroke-width='2')

  <!--#app(@touchend="unselectBtn")-->
    <!--.amount(@mousedown="changeTipPercent()") Bill ${{ checkAmount }} + {{ tipPercentValues[tipPercentIndex] }}%(${{ tipAmount }}) = ${{ newAmount }}-->
    <!--//.dial(ref="Dial", @mouseup="unselectDial", @mousedown="selectDial",@mousemove="moveDial", @mouseleave="unselectDial")-->
    <!--&lt;!&ndash;.box(ref="Box", @mouseleave="unselectDot", @mouseup="unselectDot", @mousemove="moveDot", @touchend="unselectDot", @touchmove="moveDot")&ndash;&gt;-->
      <!--&lt;!&ndash;.adding&ndash;&gt;-->
      <!--&lt;!&ndash;.dot(ref="dot", @mousedown="selectDot", @mouseup="unselectDot", @touchstart="selectDot", @touchend="unselectDot")&ndash;&gt;-->
      <!--&lt;!&ndash;.subtracting&ndash;&gt;-->
    <!--.btn-container-->
      <!--.btn(@mousedown="btnProcessInput(1)") 1-->
      <!--.btn(@mousedown="btnProcessInput(2)") 2-->
      <!--.btn(@mousedown="btnProcessInput(3)") 3-->
    <!--.btn-container-->
      <!--.btn(@mousedown="btnProcessInput(4)") 4-->
      <!--.btn(@mousedown="btnProcessInput(5)") 5-->
      <!--.btn(@mousedown="btnProcessInput(6)") 6-->
    <!--.btn-container-->
      <!--.btn(@mousedown="btnProcessInput(7)") 7-->
      <!--.btn(@mousedown="btnProcessInput(8)") 8-->
      <!--.btn(@mousedown="btnProcessInput(9)") 9-->
    <!--.btn-container-->
      <!--.btn.decimal(@mousedown="btnProcessInput('.')") .-->
      <!--.btn(@mousedown="btnProcessInput(0)") 0-->
      <!--.btn(@mousedown="btnBack") <- -->
    <!--.btn-container-->
      <!--.btn(@mousedown="btnClear") Clear-->
      //.toggle.btn(ref="toggle" @mousedown="toggleAmount") Add

</template>

<script>
import HelloWorld from './components/HelloWorld.vue'

export default {
  name: 'app',
  data () {
    return {
      amount: 0,
      selectedDial: false,
      lastX: 0,
      lastY: 0,
      dialDeg: 0,
      dialRotateAmount: 5,
      dotPos: { x: 180, y: 130 },
      selectedDot: false,
      dot: {
        height: 0,
        width: 0
      },
      toggle: true,
      checkAmount: '',
      tipAmount: '',
      newAmount: '',
      tipPercentValues: [ '5', '10', '15', '20' ],
      tipPercentIndex: 3
    }
  },
  methods: {
    addAmount (amount) {
      this.amount += amount;
    },
    removeAmount (amount) {
      if (this.amount > 0) {
        this.amount = ((this.amount - amount) < 0 ? 0 : this.amount - amount);
      }
    },
    toggleAmount () {
      if (!this.toggle){
        this.$refs.toggle.innerText = 'Add';
        this.toggle = true;
      }else if (this.toggle){
        this.$refs.toggle.innerText = 'Subtract';
        this.toggle = false;
      }
    },
    changeTipPercent() {
      this.tipPercentIndex = this.tipPercentIndex < (this.tipPercentValues.length - 1) ? this.tipPercentIndex += 1 : 0;
      if (this.checkAmount !== '') this.btnProcessInput();
    },
    btnProcessInput(value = '') {
      this.checkAmount = this.checkAmount + '' + value;
      const percentAmount = (this.tipPercentValues[this.tipPercentIndex] * 1) * 0.01;
      this.tipAmount = (parseFloat(this.checkAmount) * percentAmount).toFixed(2);
      this.newAmount = (this.tipAmount * 1 + this.checkAmount * 1).toFixed(2);
    },
    unselectBtn(event) {
      console.log('TOUCH END: ', event);
    },
    btnBack() {
      if (this.checkAmount !== '') {
        this.checkAmount = this.checkAmount.toString().substring(0, this.checkAmount.length - 1);
        this.btnProcessInput();
      }
    },
    btnClear () {
      this.checkAmount = '';
      this.tipAmount = '';
      this.newAmount = '';
    },
    btnCalTip() {
      this.amount = (parseFloat(this.checkAmount) * .2).toFixed(2) + parseFloat(this.checkAmount);
    },
    btnAmount (amount) {
      if (this.toggle) {
        this.addAmount(amount);
      }else if (!this.toggle) {
        this.removeAmount(amount);
      }
    },
    unselectDial () {
      this.selectedDial = false;
    },
    selectDial (event) {
      this.selectedDial = true;
      this.lastX = event.screenX;
      this.lastY = event.screenY;
    },
    moveDial (event) {
      if (this.selectedDial) {
        if (this.lastX < event.screenX) {
          this.rotateDial(false);
          this.addAmount(1);
        }

        if (this.lastX > event.screenX) {
          this.rotateDial(true);
          this.removeAmount(1);
        }

        if (this.lastY < event.screenY) {
          this.rotateDial(false);
          this.addAmount(1);
        }

        if (this.lastY > event.screenY) {
          this.rotateDial(true);
          this.removeAmount(1);
        }

        this.lastX = event.screenX;
        this.lastY = event.screenY;
      }
    },
    rotateDial (inverse) {
      const temp = inverse ? (this.dialRotateAmount * -1) : this.dialRotateAmount;
      this.dialDeg += temp;
      this.$refs.Dial.style.transform = `rotate(${this.dialDeg}deg)`;
    },
    selectDot () {
      this.selectedDot = true;
      const { height, width } = this.$refs.dot.getBoundingClientRect();
      //const { x, y } = this.$refs.dot.parentNode.getBoundingClientRect();
      this.dot.height = height;
      this.dot.width = width;
    },
    unselectDot () {
      this.selectedDot = false;
      this.$refs.dot.style.transform = `translate(180px, 130px)`;
    },
    moveDot (event) {
      if (this.selectedDot) {
        const { width, height } = this.dot;
        const x = (event.offsetX || event.touches[0].clientX) - width;// - this.$refs.Box.getBoundingClientRect().x;
        const y = (event.offsetY || event.touches[0].clientY) - height;// - this.$refs.Box.getBoundingClientRect().y;
        this.$refs.dot.style.transform = `translate(${ x }px, ${ y }px)`;
        if (x > this.dotPos.x) {
          this.addAmount(5);
        }
        if (x < this.dotPos.x) {
          this.removeAmount(5);
        }
        if (y < this.dotPos.y) {
          this.addAmount(5);
        }
        if (y > this.dotPos.y) {
          this.removeAmount(5);
        }
        this.dotPos.x = x;
        this.dotPos.y = y;
      }
    }
  },
  components: {
    HelloWorld
  }
}
</script>

<style lang="scss">
  .counter {
    position: absolute;
    top: 50%;
    left: 50%;
    overflow: visible;
    transform: scale(5) rotate(-90deg);
    transform-origin: center;
    .underlay {
      stroke: red;
    }
    .progress {
      stroke: red;
      stroke-dasharray: 106.814;
      stroke-dashoffset: 0;
      animation: foo 2s infinite alternate;
    }
  }
  @keyframes foo {
    from {
      stroke-dashoffset: 106.814;
    }
    to {
      stroke-dashoffset: 0;
    }
  }
</style>
