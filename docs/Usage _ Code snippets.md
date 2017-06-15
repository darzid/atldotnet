# Library usage / Code snippets

[Reading an audio file (audio data & single-tag)](#base)
[Reading an audio file (audio data & multiple tags)](#multipleTags)
[Reading audio data only](#audioOnly)
[Reading embedded pictures in a tag](#embeddedPics)
[Reading playlist contents](#playlist)
[Reading CUE Sheet contents](#cue)
[Listing supported file formats](#listSupport)
[Receiving ATL logs in your app](#logging)

{anchor:base}
## Reading an audio file (audio data & single-tag)
{{
using ATL.AudioReaders;
using System;

/* Set options for Metadata reader behaviour - this only needs to be done once, or not at all if relying on default settings */

MetaReaderFactory.GetInstance().CrossReading = false;                            // default behaviour anyway

MetaReaderFactory.GetInstance().SetTagPriority(MetaReaderFactory.TAG_APE, 0);    // Most important tagging standard, will be exclusively read if found

MetaReaderFactory.GetInstance().SetTagPriority(MetaReaderFactory.TAG_ID3V2, 1);  // 2nd most important, will be exclusively read if no APEtag is found

MetaReaderFactory.GetInstance().SetTagPriority(MetaReaderFactory.TAG_ID3V1, 2);  // 3rd most important, will be exclusively read if no ID3v2 is found

/* end Set options */

// Load audio file information into memory
Track theTrack = new Track("../../Resources/01 - Title Screen.mp3");

System.Console.WriteLine(theTrack.Duration);
System.Console.WriteLine(theTrack.Bitrate);
System.Console.WriteLine(theReader.IsVBR);
System.Console.WriteLine(AudioReaderFactory.CF_LOSSY == theTrack.CodecFamily);
System.Console.WriteLine(theTrack.Title);
System.Console.WriteLine(theTrack.Artist);
System.Console.WriteLine(theTrack.Album);
System.Console.WriteLine(theTrack.Year);
System.Console.WriteLine(theTrack.TrackNumber);
System.Console.WriteLine(theTrack.DiscNumber);
System.Console.WriteLine(theTrack.Genre);
System.Console.WriteLine(theTrack.Comment);
}}


{anchor:multipleTags}
## Reading an audio file (audio data & multiple tags)
{{
using ATL.AudioReaders;
using System;

/* Set options for Metadata reader behaviour - this only needs to be done once, or not at all if relying on default settings */

MetaReaderFactory.GetInstance().CrossReading = true;							 // Must be manually set, since default value is false

MetaReaderFactory.GetInstance().SetTagPriority(MetaReaderFactory.TAG_APE, 0);    // APEtag is the primary source of field information

MetaReaderFactory.GetInstance().SetTagPriority(MetaReaderFactory.TAG_ID3V1, 1);  // If value taken fom APEtag is empty, ATL looks for it in ID3v1 tag

MetaReaderFactory.GetInstance().SetTagPriority(MetaReaderFactory.TAG_ID3V2, 2);  // If value taken fom ID3v1 tag is empty, ATL looks for it in ID3v2 tag

/* end set options */

// Load audio file information into memory
Track theTrack = new Track("../../Resources/01 - Title Screen.mp3");

System.Console.WriteLine(theTrack.Duration);
System.Console.WriteLine(theTrack.Bitrate);
System.Console.WriteLine(theReader.IsVBR);
System.Console.WriteLine(AudioReaderFactory.CF_LOSSY == theTrack.CodecFamily);
System.Console.WriteLine(theTrack.Title);
System.Console.WriteLine(theTrack.Artist);
System.Console.WriteLine(theTrack.Album);
System.Console.WriteLine(theTrack.Year);
System.Console.WriteLine(theTrack.TrackNumber);
System.Console.WriteLine(theTrack.DiscNumber);
System.Console.WriteLine(theTrack.Genre);
System.Console.WriteLine(theTrack.Comment);
}}

{anchor:audioOnly}
## Reading audio data only
{{
using ATL.AudioReaders;
using System;

IAudioDataReader theReader = AudioReaders.AudioReaderFactory.GetInstance().GetDataReader("../../Resources/mustang_12kHz.flac");

theReader.ReadFromFile("../../Resources/mustang_12kHz.flac");

System.Console.WriteLine(theReader.Duration);
System.Console.WriteLine(theReader.BitRate);
System.Console.WriteLine(theReader.IsVBR);
System.Console.WriteLine(AudioReaderFactory.CF_LOSSLESS == theReader.CodecFamily);
}}

{anchor:pics}
## Reading embedded pictures in a tag
{{
using ATL.AudioReaders;
using System;
using System.Drawing;

Track theTrack = new Track("../../Resources/01 - Title Screen_pic.mp3");

Image picture = theTrack.GetEmbeddedPicture();
}}
{anchor:playlist}
## Reading playlist contents
{{
using ATL.PlaylistReaders;

IPlaylistReader theReader = PlaylistReaders.PlaylistReaderFactory.GetInstance().GetPlaylistReader("../../Resources/playlist.m3u");

foreach (string s in theReader.GetFiles())
{
	System.Console.WriteLine(s);
}
}}

{anchor:cue}
## Reading CUE Sheet contents
{{
using ATL.CatalogDataReaders;

ICatalogDataReader theReader = CatalogDataReaderFactory.GetInstance().GetCatalogDataReader("../../Resources/duck hunt.cue");

System.Console.WriteLine(theReader.Artist);
System.Console.WriteLine(theReader.Title);
foreach(Track t in theReader.Tracks)
{
   System.Console.WriteLine(">" + t.Title);
}
}}

{anchor:listSupport}
## Listing supported file formats
{{
using System;
using System.Text;
using ATL.AudioReaders;

StringBuilder filter = new StringBuilder("");

foreach (Format f in ATL.PlaylistReaders.PlaylistReaderFactory.GetInstance().getFormats())
{
	if (f.Readable)
	{
		foreach (String extension in f)
		{
			filter.Append(extension).Append(";");
		}
	}
}
// Removes the last separator
filter.Remove(filter.Length - 1, 1);
}}

{anchor:logging}
## Receiving ATL logs in your app

{{
using ATL.Logging;
using System.Collections.Generic;


public class LoggingTest : ILogDevice
{
	Log theLog = new Log();
	IList<Log.LogItem> messages = new List<Log.LogItem>();

	public LoggingTest()
	{
		LogDelegator.SetLog(ref theLog);
		theLog.Register(this);
	}

	public void TestSyncMessage()
	{
		messages.Clear();

		LogDelegator.GetLogDelegate()(Log.LV_DEBUG, "test message");

		System.Console.WriteLine(messages[0](0).Message);
	}

	public void DoLog(Log.LogItem anItem)
	{
		messages.Add(anItem);
	}
}
}}