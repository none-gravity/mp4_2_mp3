# mp4_2_mp3.py
from moviepy.editor import VideoFileClip
import os
import argparse

def convert_mp4_to_mp3(input_path, output_path):
    """Converts all MP4 files in a directory to MP3.

    Args:
        input_path: Path to the directory containing MP4 files.
        output_path: Path to the directory to save MP3 files (optional, defaults to input_path).
    """
    try:
        listdir = os.listdir(input_path)
        mp4_filenames = [name for name in listdir if name.endswith(".mp4")]

        if len(output_path) > 0 and not os.path.exists(output_path):
            os.makedirs(output_path) # Create output directory only if needed

        for mp4_file in mp4_filenames:
            mp4_filepath = os.path.join(input_path, mp4_file)
            mp3_filename = mp4_file[:-4] + ".mp3"
            output_filepath = os.path.join(output_path or input_path, mp3_filename) #Simplified output path logic

            if os.path.exists(output_filepath):
                print(f"Skipping {mp3_filename} - file already exists.")
                continue

            try:
                video = VideoFileClip(mp4_filepath)
                audio = video.audio
                audio.write_audiofile(output_filepath)
                print(f"Successfully converted {mp4_file} to {mp3_filename}")
            except FileNotFoundError:
                print(f"Error: MP4 file not found at {mp4_filepath}")
            except Exception as e:
                print(f"An error occurred while processing {mp4_file}: {e}")
            finally:
                if 'video' in locals() and video:
                    video.close()
                if 'audio' in locals() and audio:
                    audio.close()


    except FileNotFoundError:
        print(f"Error: Input directory not found at {input_path}")
    except Exception as e:
        print(f"An unexpected error occurred: {e}")


if __name__ == "__main__":
    parser = argparse.ArgumentParser(description="Batch convert MP4 files to MP3")
    parser.add_argument("-i", "--input_path", default="./mp4", help="Path to the directory containing MP4 files")
    parser.add_argument("-o", "--output_path", default="./mp3", help="Path to save MP3 files (optional)")
    args = parser.parse_args()

    convert_mp4_to_mp3(args.input_path, args.output_path)