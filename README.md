
import os

# 在类内部添加状态检查方法
def _is_vision_blocked(self):
    # 如果文件存在，表示允许图片；文件不存在，表示屏蔽
    return not os.path.exists("data/plugins/vision_allowed.flag")

# 拦截用户图片时调用
@filter.event_message_type(filter.EventMessageType.ALL)
async def block_user_image(self, event: MessageEventResult):
    if self._is_vision_blocked() and event.message_obj and any(seg.type == 'image' for seg in event.message_obj.message):
        event.stop_event()
        await event.send("🚫 视觉功能已禁用。在终端执行 touch data/plugins/vision_allowed.flag 可开启。")

# 拦截LLM请求时调用
@filter.on_llm_request()
async def block_llm_vision(self, request: ProviderRequest):
    if self._is_vision_blocked() and request.image_urls:
        request.image_urls = []
