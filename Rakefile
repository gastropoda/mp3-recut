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

fragment_names.each do |labels_path, fragments|
  mp3 = labels_path.sub(".txt", ".mp3")
  fragments.each do |fragment|
    fragment_mp3 = "#{fragment}.mp3"
    file fragment_mp3 => [labels_path, mp3] do
      sh %Q[mp3splt "#{mp3}" -A "#{labels_path}"]
    end
    task :default => fragment_mp3
  end
end
