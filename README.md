"use client";

import { useState } from 'react';

export default function Home() {
  const [videoUrl, setVideoUrl] = useState('');
  const [downloadLink, setDownloadLink] = useState('');
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState('');

  const handleDownload = async (e: React.FormEvent) => {
    e.preventDefault();
    if (!videoUrl) return;

    setLoading(true);
    setError('');
    setDownloadLink('');

    try {
      // لێرەدا لە هەنگاوەکانی داهاتوودا بەستی دەکەینەوە بە API ڕاستەقینە
      const response = await fetch('/api/download', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ url: videoUrl }),
      });

      const data = await response.json();
      if (data.success) {
        setDownloadLink(data.download_url);
      } else {
        setError('نەتوانرا لینکی داونلۆد بەدەست بهێنرێت. دڵنیابەوە لە لینکەکە.');
      }
    } catch (err) {
      setError('هەڵەیەک لە پەیوەندی سێرڤەردا ڕوویدا!');
    } finally {
      setLoading(false);
    }
  };

  return (
    <div className="min-h-screen bg-gradient-to-b from-gray-900 to-black text-white flex flex-col items-center justify-center p-4" dir="rtl">
      <div className="w-full max-w-xl text-center space-y-6">
        <h1 className="text-4xl font-extrabold bg-clip-text text-transparent bg-gradient-to-r from-blue-400 to-purple-500">
          داونلۆدکەری ڤیدیۆی سۆشیال میدیا
        </h1>
        <p className="text-gray-400 text-sm md:text-base">
          لینکی ڤیدیۆکانی (TikTok, Instagram, Facebook) دابنێ بۆ داونلۆدکردن بەبێ لۆگۆ
        </p>

        <form onSubmit={handleDownload} className="space-y-4">
          <div className="relative">
            <input
              type="text"
              placeholder="لینکی ڤیدیۆکە لێرە دابنێ..."
              value={videoUrl}
              onChange={(e) => setVideoUrl(e.target.value)}
              className="w-full p-4 pr-4 rounded-xl bg-gray-800 border border-gray-700 text-white placeholder-gray-500 focus:outline-none focus:ring-2 focus:ring-purple-500 text-left"
            />
          </div>
          
          <button
            type="submit"
            disabled={loading}
            className="w-full bg-gradient-to-r from-blue-600 to-purple-600 hover:from-blue-700 hover:to-purple-700 text-white font-bold p-4 rounded-xl shadow-lg transition duration-200 disabled:opacity-50"
          >
            {loading ? 'چاوەڕوانبە / شیکردنەوەی لینک...' : 'وەرگرتنی ڤیدیۆ'}
          </button>
        </form>

        {error && <p className="text-red-500 text-sm">{error}</p>}

        {downloadLink && (
          <div className="mt-8 p-6 bg-gray-800 rounded-xl border border-gray-700 animate-fade-in">
            <h3 className="text-lg font-bold mb-4 text-green-400">ڤیدیۆکە ئامادەیە!</h3>
            <a
              href={downloadLink}
              target="_blank"
              rel="noopener noreferrer"
              className="inline-block w-full bg-green-600 hover:bg-green-700 text-white font-bold py-3 px-6 rounded-xl transition duration-200"
            >
              داونلۆدکردن بەبێ لۆگۆ
            </a>
          </div>
        )}
      </div>
    </div>
  );
}
