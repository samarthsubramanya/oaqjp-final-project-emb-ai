"""Serve the emotion detection application using Flask."""

from pathlib import Path

from flask import Flask, render_template, request

from EmotionDetection import emotion_detector


PROJECT_ROOT = Path(__file__).resolve().parent.parent
app = Flask(
    __name__,
    template_folder=str(PROJECT_ROOT / "templates"),
    static_folder=str(PROJECT_ROOT / "static"),
)


@app.route("/")
def index():
    """Display the provided application interface."""
    return render_template("index.html")


@app.route("/emotionDetector", methods=["GET"])
def detect_emotion():
    """Analyze the submitted text and format the emotion scores."""
    text_to_analyze = request.args.get("textToAnalyze", "")
    result = emotion_detector(text_to_analyze)
    if result["dominant_emotion"] is None:
        return "Invalid text! Please try again!"

    return (
        "For the given statement, the system response is "
        f"'anger': {result['anger']}, "
        f"'disgust': {result['disgust']}, "
        f"'fear': {result['fear']}, "
        f"'joy': {result['joy']} and "
        f"'sadness': {result['sadness']}. "
        f"The dominant emotion is <strong>{result['dominant_emotion']}</strong>."
    )


if __name__ == "__main__":
    app.run(host="127.0.0.1", port=5000)
