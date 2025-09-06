import json
import requests
from typing import Dict, List, Optional


class LongCatChat:
    """A class to interact with the LongCat chat API."""

    def __init__(self, api_url: str = "https://longcat.chat/api/v1/chat-completion-oversea"):
        """
        Initialize LongCatChat instance.

        Args:
            api_url (str): API endpoint URL
        """
        self.api_url = api_url
        self.headers = {
            "accept": "text/event-stream,application/json",
            "content-type": "application/json",
            "m-appkey": "fe_com.sankuai.friday.fe.longcat",
            "x-client-language": "en"
        }
        self.conversation_id = None
        self.message_history: List[Dict] = []

    def send_message(
        self,
        content: str,
        enable_reason: bool = False,
        enable_search: bool = False,
        regenerate: bool = False
    ) -> str:
        """
        Send a message to the chat API.

        Args:
            content (str): Message content to send

        Returns:
            str: Response from the API
        """
        payload = {
            "content": content,
            "messages": self._prepare_messages(content),
            "reasonEnabled": 1 if enable_reason else 0,
            "searchEnabled": 1 if enable_search else 0,
            "regenerate": 1 if regenerate else 0
        }
        response = requests.post(
            self.api_url,
            headers=self.headers,
            json=payload,
            stream=True
        )

        full_response = ""
        for line in response.iter_lines():
            if line:
                data = json.loads(line.decode('utf-8').replace('data:', ''))
                if 'choices' in data and data['choices'][0]['delta'].get('content'):
                    full_response += data['choices'][0]['delta']['content']
                if not self.conversation_id and data.get('conversationId'):
                    self.conversation_id = data['conversationId']

        self._update_history(content, full_response)
        return full_response

    def _prepare_messages(self, content: str) -> List[Dict]:
        """
        Prepare message history for API request.

        Args:
            content (str): Current message content

        Returns:
            List[Dict]: Formatted message history
        """
        messages = self.message_history.copy()
        messages.append({
            "role": "user",
            "content": content,
            "chatStatus": "FINISHED",
            "messageId": len(messages) * 2 + 1,
            "idType": "custom"
        })
        messages.append({
            "role": "assistant",
            "content": "",
            "chatStatus": "LOADING",
            "messageId": len(messages) * 2 + 2,
            "idType": "custom"
        })
        return messages

    def _update_history(self, user_msg: str, assistant_msg: str) -> None:
        """
        Update message history after exchange.

        Args:
            user_msg (str): User's message
            assistant_msg (str): Assistant's response
        """
        self.message_history.append({
            "role": "user",
            "content": user_msg,
            "chatStatus": "FINISHED",
            "messageId": len(self.message_history) * 2 + 1,
            "idType": "custom"
        })
        self.message_history.append({
            "role": "assistant",
            "content": assistant_msg,
            "chatStatus": "FINISHED",
            "messageId": len(self.message_history) * 2 + 2,
            "idType": "custom"
        })

    def clear_history(self) -> None:
        """Clear chat history and conversation ID."""
        self.message_history = []
        self.conversation_id = None

if __name__ == "__main__":
    chatbot = LongCatChat()
    enable_reason = False
    enable_search = False
    while True:
        user_input = input("You: ")
        if user_input.lower() == "exit":
            break
        if user_input.lower() == "/reason":
            enable_reason = not enable_reason
            print(f"Reason is now {'enabled' if enable_reason else 'disabled'}")
            continue
        if user_input.lower() == "/search":
            enable_search = not enable_search
            print(f"Search is now {'enabled' if enable_search else 'disabled'}")
            continue
        response = chatbot.send_message(
            user_input,
            enable_reason=enable_reason,
            enable_search=enable_search
        )
        print(f"LongCat: {response}")
