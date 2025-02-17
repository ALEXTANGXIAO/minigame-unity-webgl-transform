# 输入法适配

在小游戏中Unity游戏唤不起输入法，需要使用WX_SDK中提供的方法来唤起输入法，并做简单的逻辑修改来适配。

详细示例请参考[API Demo](https://github.com/wechat-miniprogram/minigame-unity-webgl-transform/tree/main/Demo/API)

以UGUI的Input组件为例，需要给Input 绑定以下脚本：
```
public class Inputs : MonoBehaviour, IPointerClickHandler, IPointerExitHandler
{
    public InputField input;
    private bool isShowKeyboad = false;
    public void OnPointerClick(PointerEventData eventData)
    {
        Debug.Log("ooooo");
        ShowKeyboad();
    }

    public void OnPointerExit(PointerEventData eventData)
    {
        // 隐藏输入法
        if (!input.isFocused)
        {
            HideKeyboad();
        }

    }

    public void OnInput(OnKeyboardInputCallbackResult v)
    {
        Debug.Log("onInput");
        Debug.Log(v.value);
        if (input.isFocused)
        {
            input.text = v.value;
        }

    }

    public void OnConfirm(OnKeyboardInputCallbackResult v)
    {
        // 输入法confirm回调
        Debug.Log("onConfirm");
        Debug.Log(v.value);
        HideKeyboad();
    }

    public void OnComplete(OnKeyboardInputCallbackResult v)
    {
        // 输入法complete回调
        Debug.Log("OnComplete");
        Debug.Log(v.value);
        HideKeyboad();
    }

    private void ShowKeyboad()
    {
        if (!isShowKeyboad)
        {
            WX.ShowKeyboard(new ShowKeyboardOption()
            {
                defaultValue = "xxx",
                maxLength = 20,
                confirmType = "go"
            });

            //绑定回调
            WX.OnKeyboardConfirm(OnConfirm);
            WX.OnKeyboardComplete(OnComplete);
            WX.OnKeyboardInput(OnInput);
            isShowKeyboad = true;
        }
    }

    private void HideKeyboad()
    {
        if (isShowKeyboad)
        {
            WX.HideKeyboard(new HideKeyboardOption());
            //删除掉相关事件监听
            WX.OffKeyboardInput(OnInput);
            WX.OffKeyboardConfirm(OnConfirm);
            WX.OffKeyboardComplete(OnComplete);
            isShowKeyboad = false;
        }
    }

}
```
