<template>
    <div>
        <h3>I am guessing a number from 1 to 10</h3>
        <h3>You must guess what it is in three tries</h3>

        <el-tooltip class="item" effect="dark"  placement="right">
            <div slot="content">
                Cold: 3 or more away from correct answer
                <br/>
                Warm: 2 away from correct answer
                <br />
                Hot: 1 away from correct answer
            </div>
            <el-avatar size="small"><img alt="info" src="../assets/info.png"></el-avatar>
        </el-tooltip>
        
        <h3>Remaining Chances: {{ chances }}</h3> 

        <el-form :model="guessForm" :rules="rules" ref="guessForm">
            <el-form-item  id="input" prop="guess">
                <el-input v-model.number="guessForm.guess" placeholder="Enter a guess"  @keypress.enter.native.prevent></el-input>
            </el-form-item>
             <el-form-item style="margin-top: 20px">
                <el-button type="primary" @click="submitForm">Guess it</el-button>
             </el-form-item>
        </el-form>
    </div>
</template>

<script>
export default {
    name: 'Game',

    data() {
        var checkGuess = (rule, value, callback) => {
            if (!value) {
                return callback(new Error('Please enter your guess'));
            }
            setTimeout(() => {
                if (!Number.isInteger(value)) {
                    callback(new Error('Please input digits'));
                } else if (value < 1 || value > 10) {
                    callback(new Error('Your guess must be from 1 to 10'));
                } else {
                    callback();
                }
            }, 100);
        };

        return {
            randomNumber: Math.floor(Math.random() * 10 + 1),
            chances: 3,
            guessForm: {
                guess: null,
            },
            rules: {
                guess: [
                     { validator: checkGuess, trigger: 'blur' }
                ],
            },
        }
    },

    methods: {
        submitForm(e) {
            e.preventDefault();
            
            if(this.chances === 0) {
                this.createMessage(`<h2><strong>You are out of chances.<br /> Start a new Game!</strong></h2>`);

                return;
            }
        
            this.$refs['guessForm'].validate((valid) => {
                if (!valid) {
                    this.guessForm.guess = null;
                    return;
                } 

                this.submitGuess();
            });
        },

        submitGuess() {
            if (this.guessForm.guess === this.randomNumber) {
                this.createMessage(`<h2><strong>Yaay your guess is correct!.<br /> Start a new Game!</strong></h2>`);
            } else if (Math.abs(this.guessForm.guess - this.randomNumber) === 1) {
                this.displayError('Hot');
            } else if (Math.abs(this.guessForm.guess - this.randomNumber) === 2) {
                this.displayError('Warm');
            } else {
                this.displayError('Cold');
            }
            
            this.guessForm.guess = null;
            this.chances--;
        },

        createMessage(message) {
            this.$alert(message, {
                dangerouslyUseHTMLString: true,
                confirmButtonText: 'OK',
                showClose: false,
                center: true
            }).then(() => {
                window.location.reload();
            });
        },

        displayError(error) {
            this.$message.error('Your guess is:' + this.guessForm.guess + ' ' + error);  
        }
    }
}
</script>

<style scoped>
#input {
  width: 50%;
  margin: 0 auto;
}
</style>
