<script>
    import Vue from 'vue'
    import * as common from './common/common'
    import uniPopup from './components/uni-popup/uni-popup'
    import myPopupSelect from './components/my-components/my-popup-select'
    Vue.component('uni-popup',uniPopup)
    Vue.component('my-popup-select',myPopupSelect)
	export default {
        globalData: {
          // test: http://tt.962460.com/handler/api/UploadHandler.ashx?method=ProcessRequest
          // prod: http://wy.962460.com/handler/api/UploadHandler.ashx?method=ProcessRequest
          uploadFileUrl: require('./api-config').uploadFileUrl, // 'https://wy.962460.com/handler/api/UploadHandler.ashx?method=ProcessRequest',
          // 图片前缀
          // filePrefix: 'http://wy.962460.com',
          code: '',
          encryptedData: '',
          iv: '',
          jwtToken: '',
          wxUserInfo: {}
        },
		async onLaunch () {
			console.log('App Launch');
		},
		onShow: function() {
			console.log('App Show');
          uni.checkSession({
            success () {
              //session_key 未过期，并且在本生命周期一直有效
            },
            fail () {
            }
          })
		},
		onHide: function() {
			console.log('App Hide');
		}
	}
</script>

<style lang="scss">
	/* 头条小程序需要把 iconfont 样式放到组件外 */
	@import "components/m-icon/m-icon.css";
    @import "common/common.scss";
    @import "common/iconfont.css";

	/*每个页面公共css */
	page {
		min-height: 100%;
		display: flex;
		font-size: 16px;
        color: #333;
	}

	/* #ifdef MP-BAIDU */
	page {
		width: 100%;
		height: 100%;
		display: block;
	}

	swan-template {
		width: 100%;
		min-height: 100%;
		display: flex;
	}

	/* 原生组件模式下需要注意组件外部样式 */
	custom-component {
		width: 100%;
		min-height: 100%;
		display: flex;
	}

	/* #endif */

	/* #ifdef MP-ALIPAY */
	page {
		min-height: 100vh;
	}

	/* #endif */

	/* 原生组件模式下需要注意组件外部样式 */
	m-input {
		width: 100%;
		/* min-height: 100%; */
		display: flex;
		flex: 1;
	}

	.content {
		display: flex;
		flex: 1;
		flex-direction: column;
		background-color: #f1f1f1;
	}

	.input-group {
		background-color: #ffffff;
		margin-top: 20px;
		position: relative;
	}

	.input-group::before {
		position: absolute;
		right: 0;
		top: 0;
		left: 0;
		height: 1px;
		content: '';
		-webkit-transform: scaleY(.5);
		transform: scaleY(.5);
		background-color: #c8c7cc;
	}

	.input-group::after {
		position: absolute;
		right: 0;
		bottom: 0;
		left: 0;
		height: 1px;
		content: '';
		-webkit-transform: scaleY(.5);
		transform: scaleY(.5);
		background-color: #c8c7cc;
	}

	.input-row {
		display: flex;
		flex-direction: row;
		position: relative;
		font-size: 18px;
		line-height: 40px;
	}

	.input-row .title {
		width: 72px;
		padding-left: 15px;
	}

	.input-row.border::after {
		position: absolute;
		right: 0;
		bottom: 0;
		left: 8px;
		height: 1px;
		content: '';
		-webkit-transform: scaleY(.5);
		transform: scaleY(.5);
		background-color: #c8c7cc;
	}

	.btn-row {
		margin-top: 25px;
		padding: 10px;
	}
    // 覆盖样式
    button.primary {
        background-color: $uni-color-primary
    }
    button.primary.button-hover{
        background-color: $uni-color-primary;
        opacity: 0.8;
    }
</style>
