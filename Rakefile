def label_files
  Dir["*.txt"].select do |txt_path|
    File.exists?(txt_path.sub(".txt", ".mp3"))
  end
end

def fragment_names
  label_files.map { |path| [path, label_file_fragment_names(path)] }.to_h
end

def label_file_fragment_names(path)
  require "csv"
  contents = CSV.read(path, col_sep: "\t")
  contents.map { |row| row[2] }
end

def partial_chapter? base
  base[/\d[a-z]$/]
end

def multipart_chapters
  fragment_names
    .values
    .flatten
    .select { |base| partial_chapter?(base) }
    .group_by { |base| base.sub(/.$/, "") }
end

multipart_chapters.each do |chapter, parts|
  chapter_mp3 = "#{chapter}.mp3"
  chapter_wrapped_mp3 = "#{chapter}_MP3WRAP.mp3"
  parts_mp3 = parts.sort.map { |part| "#{part}.mp3" }
  file chapter_mp3 => parts_mp3 do
    quoted_parts = parts_mp3.map { |path| %Q["#{path}"] }.join(" ")
    sh %Q[mp3wrap "#{chapter_mp3}" #{quoted_parts}]
    FileUtils.mv chapter_wrapped_mp3, chapter_mp3
  end
  task :default => chapter_mp3
end

fragment_names.each do |labels_path, fragments|
  mp3 = labels_path.sub(".txt", ".mp3")
  fragments.each do |fragment|
    fragment_mp3 = "#{fragment}.mp3"
    file fragment_mp3 => [labels_path, mp3] do
      sh %Q[mp3splt "#{mp3}" -A "#{labels_path}"]
    end
    task :default => fragment_mp3 unless partial_chapter?(fragment)
  end
end
