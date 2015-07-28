pms = '/Applications/Plex\ Media\ Server.app/Contents/MacOS/Plex\ Media\ Scanner'

task :default => ['init']

desc "Run all tasks"
task :init => [
                :unrar,
                :gather,
                :date_based_shows,
                :rename,
                :plex ] do
end


desc "Create folder structure from file name"
# Match: The.ShowName.S01E01.HDTV.x264-2HD.mp4
# -> The ShowName/Season 01/The.ShowName.S01E01.HDTV.x264-2HD.mp4
# Match: Star Wars Enterprise - 0401 - Foo.avi
# ->  Star Wars Enterprise/Season 04/Star Wars Enterprise - 0401 - Foo.avi
# Match: Uptown Abbey - S03E07.avi
# Match: Uptown.Abbey.3x04.HDTV.x264-FoV.mp4
# Match: Old.Justice.Season.2.Episode.17.The.Foo.HDTV..avi
task :rename do
  files = Dir['*.{AAC,aac,AVI,avi,MKV,mkv,MP4,mp4,M4V,m4v}']
  files.each do |file|
    # Check is the directory has a file extention (gather should pick it up)
    next if File.directory?(file)
    series   = file.match(/(.*)(?=.((S|s)[0-9]|[0-9]x|- [0-9][0-9]|Season\.))/).to_s
    episode  = file.match(/((?<=E|e|Episode\.)[0-9]+|(?<=[0-9]x| - [0-9][0-9])[0-9][0-9])/).to_s
    season   = file.match(/((?<=S|s|Season\.)[0-9]+|(?<= - )[0-9][0-9]|[0-9](?=x[0-9][0-9]))/).to_s
   
    unless series.nil? or series == ''
      series_path = series.gsub('.',' ').rstrip
    end
    unless season.nil? or series == ''
      season_path = 'Season ' + season.to_i.to_s
    end
    unless episode.nil? or series == ''
      episode_path = 'Episode ' + episode
    end

    unless series_path.nil? or series_path == '' or season_path.nil? or season_path == ''
      FileUtils.mkdir_p("#{series_path}/#{season_path}/")
      FileUtils.mv(file,"#{series_path}/#{season_path}/#{file}")
      else
      cputs "#{series_path}/#{season_path}/#{file}"
    end
  end
end


desc "Extract all rar archives"
task :unrar do
  files = Dir['*/*.{rar}']
  files.each do |file|
    wrapper_dir         = File.dirname(file)
    extracted_file_name = `unrar lb '#{file}'`
    cputs "Processing: #{extracted_file_name}"

    # Don't overwrite extract
    extracted_file = system("unrar -o- -p- x '#{file}' .")
    cputs "Removing file #{file}"
    FileUtils.rm_rf(file)
  end
end

#multiple files
desc "Find all the files in subfolders"
task :gather do
  remove_array = []

  files = Dir['*/*.{AAC,aac,AVI,avi,MKV,mkv,MP4,mp4,M4V,m4v}']
  files.each do |file|
    cputs file
    wrapper_dir = File.dirname(file)
    remove_array.push(wrapper_dir)

  end
  # Now that we have copied out the files rm the dirs
  remove_array.uniq.each do |dir|
    cputs "Removing directory #{dir}"
    FileUtils.rm_rf(dir)
  end
end

desc "Trigger a plex media refresh"
task :plex do
  system "#{pms} -s"
  system "#{pms} -r"
end

desc "Manage date based shows"
# Match: The.Weekly.Show.2013.02.19.720p.HDTV.x264-LMAO.mkv
# -> The Weekly Show/2013/02/The.Weekly.Show.2013.02.19.720p.HDTV.x264-LMAO.mkv
task :date_based_shows do
  files = Dir['*.{AAC,aac,AVI,avi,MKV,mkv,MP4,mp4,M4V,m4v}']
  files.each do |file|
    if file =~ /\.\d\d\d\d\.\d\d\.\d\d\./
      series  = file.match(/(.*)(?=\.\d\d\d\d)/).to_s
      date    = file.scan(/(\d{4})\.(\d{2})\.(\d{2})/)[0]
      year    = date[0]
      month   = date[1]
      day     = date[2]
      unless series.nil? or series == ''
        series_path = series.gsub('.',' ').rstrip
      end
      cputs "#{series_path}/#{year}/#{month}/#{file}"
      FileUtils.mkdir_p("#{series_path}/#{year}/#{month}/")
      FileUtils.mv(file,"#{series_path}/#{year}/#{month}/")
    end
  end
end

def cputs(string)
  puts "\033[1m#{string}\033[0m"
end
